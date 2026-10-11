# hr_attendance migration guide (18.0 -> 19.0)

## What's new for users

**Overtime rulesets.** Extra hours are no longer driven by one fixed threshold logic. You now define *rules* and group them into *rulesets* that describe how extra hours are earned and compensated. A ruleset is attached through the employee's contract version, so two contracts can follow different overtime policies. Overtime is recomputed week by week, per employee and per day.

**Overtime approved on the overtime entries.** Approving or refusing extra hours now happens on the overtime lines themselves; the "Extra Hours" value shown on an attendance becomes a read-only summary.

**More precise check-in location.** Location is now based on GPS coordinates when available (reverse-geocoded into a place name) and falls back to the IP address. The old Country + City couple is replaced by a single **Location** field for check-in and check-out.

**Disable GPS and IP tracking.** Tracking can now be turned off for privacy-sensitive deployments.

**Kiosk presence indicator.** A presence indicator is displayed during manual employee selection on the kiosk.

**Automatic checkout.** Auto checkout and absence management are now disabled for working schedules with flexible hours.

*Note:* the release notes' "time off ledger" and payroll overtime items belong to other apps, not to this addon.

## Technical data model changes

- New models: `hr.attendance.overtime.ruleset` and `hr.attendance.overtime.rule` (rulesets expose `rule_ids`).
- `hr.attendance.overtime` is reworked into `hr.attendance.overtime.line` (one line per employee/day) with a `status` (to approve / approved / refused) and a `manual_duration`.
- Removed fields: `duration_real` and `adjustment` (overtime), `no_validated_overtime_hours` (attendance).
- `hr.attendance` gains a stored, indexed, required `date` (computed from `check_in` in the employee's timezone) and `linked_overtime_ids` (Many2many to overtime lines).
- `validated_overtime_hours` ("Extra Hours") is now stored and **read-only** (it was editable in 18.0).
- Geolocation: `in_city`, `in_country_name`, `out_city`, `out_country_name` removed, replaced by `in_location` / `out_location`.
- `res.users`: removal of the related fields `hours_last_month`, `hours_last_month_display`, `total_overtime`, `hours_last_month_overtime`, `attendance_manager_id`, `display_extra_hours`, `attendance_state`, `last_check_in`, `last_check_out`, plus `action_open_last_month_attendances`; the hr_attendance user "simple modif" view is removed/merged.
- Method changes: `_update_overtime()` now takes a domain (instead of a dict of employee attendance dates) and delegates to rules; `_get_attendances_dates()` and `_get_pre_post_work_time()` are gone; new helpers `_get_week_date_range()`, `_get_overtimes_to_update_domain()`, `_compute_date()`, `_compute_linked_overtime_ids()`, `_linked_overtimes()`. `action_approve_overtime()` / `action_refuse_overtime()` now act on the linked overtime lines. A new `hr.version._get_versions_by_employee_and_date()` selects the applicable ruleset.

## How your habits should change

- Design your overtime policy as rulesets/rules and assign them to contract versions instead of relying on the previous automatic threshold computation only.
- Approve or refuse extra hours from the overtime entries; hand-editing "Extra Hours" is no longer possible.
- Any change to a check-in or check-out triggers a recomputation for the whole week for that employee, and only attendances falling inside a valid contract version are considered.
- Attendance statistics and the attendance manager are no longer available on the user preferences/profile form.
- Saved filters, exports or reports using City/Country on attendances must be switched to the single Location field.

## What you gain by migrating

- A genuinely customizable overtime policy per contract, with weekly, per-day computation.
- A clearer approval workflow: traceable overtime lines with their own status instead of a field on the attendance.
- More accurate, GPS-based check-in/check-out locations.
- Explicit control over GPS and IP tracking.
- A better kiosk experience and consistent behaviour of automatic checkout with flexible schedules.
- You stay on a supported version with a maintained upgrade path from 18.0.
