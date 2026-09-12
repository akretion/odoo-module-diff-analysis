# hr_attendance migration guide (18.0 -> 19.0)

This guide covers what changes in the Attendance app (Community edition) between Odoo 18.0 and 19.0.

## What's new for users

- **Overtime rulesets** — Define your own overtime policy instead of relying on fixed thresholds. A ruleset contains rules based on **Quantity** (hours beyond a day/week target) or **Timing** (working days, non-working days, leave, or outside a given schedule), each with tolerances, an optional "hours from employee schedule", and a pay rate. The ruleset is attached to the employee's contract version. Rates are stored in the Community app; paying them out requires the Enterprise Payroll app.
- **Officer access rights** — attendance officers can consult all attendance records and reports without configuration/settings rights.
- **GPS-based location** — check-in/out location is computed from GPS coordinates when available, falling back to the IP address. The separate City/Country columns become one "Location" value.
- **Kiosk** — a presence indicator is now shown during manual employee selection, and the Kiosk / employee profile UI has been reworked.
- **Automatic checkout** — automatic checkout and absence creation are disabled for flexible-hour working schedules.

## Technical data model changes

**hr.attendance**
- New stored, indexed, required `date` field, computed from Check In in the employee's timezone.
- `in_city`, `in_country_name`, `out_city`, `out_country_name` removed, replaced by `in_location` / `out_location`.
- `validated_overtime_hours` is now read-only (computed from approved overtime lines); `no_validated_overtime_hours` removed.
- New `linked_overtime_ids` (many2many to overtime lines).
- Method signatures: `_update_overtime(attendance_domain=None)` replaces the `employee_attendance_dates` argument; `_get_attendances_dates()` and `_get_overtime_leave_domain()` removed; recomputation is now done week by week (`_get_week_date_range`).

**Overtime**
- `hr.attendance.overtime` is replaced by `hr.attendance.overtime.line`: `duration_real` and `adjustment` disappear; a line carries `time_start`, `time_stop`, `duration`, `manual_duration`, `status` (to approve / approved / refused), `amount_rate` and `rule_ids`. The "one overtime per employee per day" unique index is gone.
- New models `hr.attendance.overtime.rule` and `hr.attendance.overtime.ruleset` (with `rate_combination_mode`: max or sum) and `action_regenerate_overtimes`.
- `hr.version` gains `ruleset_id` and a version lookup by employee/date; `hr.employee` gains `ruleset_id` (related to the version).

**res.users / hr.employee**
- Removed from `res.users`: `hours_last_month`, `hours_last_month_display`, `total_overtime`, `hours_last_month_overtime`, `attendance_state`, `last_check_in`, `last_check_out`, `attendance_manager_id`, `display_extra_hours`, and the actions `action_open_last_month_attendances` / `action_open_last_month_overtime`. Those actions now live on the employee (and public employee) records.
- `hr.employee.get_overtime_data()` added; the presence icon/state computes moved from `hr.employee.base` to `hr.employee`.

## How your habits should change

- Overtime is no longer a single daily total but a set of timed lines. Approve or refuse each line; an attendance's overtime status is derived from the lines covering its day.
- To correct extra hours, edit the line's "Extra Hours (encoded)" field instead of the old daily record or "adjustment" entries.
- Validated extra hours can no longer be typed on the attendance — they are computed from approved lines.
- Configure the policy on a ruleset, attach it to the employee's contract version, then use **Regenerate Overtimes** after changing rules.
- Overtime for a day is recomputed with the other attendances of the same week and employee, so editing one attendance may update nearby days.
- Use the Location column instead of City/Country in exports and reports.
- Look for attendance drill-downs on the employee form, not on user preferences.

## What you gain by migrating

- A real overtime policy engine, configurable per country/company and per contract version, instead of fixed thresholds.
- Time-stamped, individually approvable overtime lines with pay rates: a dependable base for payroll.
- More accurate where/when data (GPS location, stored attendance date) for reporting and filtering.
- Officer rights for team leads without granting administrator access.
- A cleaner data model: fewer duplicated fields on users, attendance history where it belongs, on the employee.

If you use custom code or reports touching `hr.attendance.overtime`, `res.users` attendance fields, or the old overtime methods, plan a review before the upgrade.
