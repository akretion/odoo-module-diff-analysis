# resource migration guide (19.0 -> 20.0)

## What's new for users

No relevant official 20.0 release note was available for this addon, so this summary is based on the Odoo 20.0 source history (PRs #229706 and #246700).

Two large working-schedule changes land in 20.0:

- **Variable working schedules.** A calendar is now typed: *Fixed* (the classic weekly pattern) or *Variable* (attendances bound to actual dates, with recurrence rules — every N days or weeks, ending on a date or never, with individually excluded occurrences). This replaces the old "2 weeks calendar".
- **Simpler fixed schedules.** The schedule-type chooser is gone: no more "Flexible vs Fully Fixed" toggle, no more two-week mode, no more calendar-level "duration based" flag, no more lunch period. Working time lines always define an amount of hours per day.
- **Timezone moved to the company.** A working schedule no longer carries its own timezone; the timezone comes from the company (and per resource, from the employee/version). When the admin user's timezone is set at first login, it now updates the company instead of the calendar.
- Attendances no longer have a name, a "duration in days" or a day period; an employee who does not clock out for lunch is now simply counted as working through it.

## Technical data model changes

`resource.calendar`
- Removed fields: `two_weeks_calendar`, `two_weeks_explanation`, `attendance_ids_1st_week`, `attendance_ids_2nd_week`, `schedule_type`, `flexible_hours`, `duration_based`, `tz`, `tz_offset`.
- Added fields: `calendar_type` (`fixed` / `variable`, default `fixed`) and `days_per_week`. `hours_per_day` is now derived from `hours_per_week / days_per_week`; both stored with 5 decimals.
- Removed methods/actions: `switch_calendar_type`, `switch_based_on_duration`, `_check_overlap`, `_compute_two_weeks_attendance`, `_inverse_two_weeks_calendar`, `get_attendances`, `_get_working_hours`, `_get_two_weeks_attendance`.
- New helpers: `_get_working_attendances`, `_get_attendances_by_date`, `_get_attendances_to_unlink`, `_calendar_clean_up`, `is_calendar_referenced`.

`resource.calendar.attendance`
- Removed fields: `name`, `duration_days`, `day_period`, `week_type`.
- `hour_from` / `hour_to` are no longer plain required inputs: they are computed and stored.
- New fields for the variable calendar: `date`, `recurrency`, `recurrency_type`, `recurrency_interval`, `recurrency_until`, `recurrency_end_type`, `recurrency_excluded_occurences`. `duration_based` becomes computed/stored.

`res.company`
- Added `tz` (required) — the timezone previously held on the calendar.

`res.users`
- On first admin login, `tz` now propagates to the company, not the calendar.

API signature changes
- `_attendance_intervals_batch(start_dt, end_dt, resources_per_tz=None, domain=None)`: the `tz` and `lunch` arguments are gone; resources are grouped in a timezone dictionary.
- `_leave_intervals_batch` and `_work_intervals_batch` take `resources_per_tz` instead of `resources` / `tz`.

## How your habits should change

- Choose the **calendar type** first: Fixed for regular weekly patterns, Variable when days differ or follow a rotation.
- Rebuild any two-week / alternating schedule as a Variable calendar with a recurrence (e.g. every 2 weeks).
- Stop looking for a timezone on the working schedule: set it on the company, or on the employee/contract.
- Remove lunch breaks from your mental model — attendance lines only express hours worked; a lunch gap must be an explicit unpaid period or a shorter attendance.
- If you used "flexible hours" calendars, rely on the resource/employee level hours per week/day instead.
- Review custom code and integrations calling `_attendance_intervals_batch`, `_work_intervals_batch`, `_leave_intervals_batch` or `get_attendances`: signatures changed and `get_attendances` is removed.

## What you gain by migrating

- One coherent working-schedule model, without the old flexible / two-week special cases.
- Rotations and irregular schedules handled natively with recurrences and excluded dates, instead of duplicated lines.
- Cleaner timezone handling: one company-level timezone, no per-calendar drift.
- Simpler attendance lines (hours only), making imports, integrations and payroll or attendance exports more predictable.
