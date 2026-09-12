# resource migration guide (18.0 -> 19.0)

The `resource` addon is the shared foundation behind working schedules (Working Hours), resources and time-off/leave computations. It has no menu of its own, so its changes surface everywhere calendars are used: Employees, Time Off, Planning and Appointments. All changes below are core (Community) changes.

## What's new for users

The 19.0 release notes contain **no dedicated section for the resource addon**. The closest items ("Flexible appointment scheduling", "Flexible appointments: duration display", "Auto-plan on flexible schedules") belong to the Appointments and Planning apps, not to resource; "Auto-plan on flexible schedules" is Planning, which is **not** part of the Community edition, so do not count on it. The details below therefore come from the code, not from the release notes.

What you will notice in the Working Hours form:
- A schedule can now be "attendance based on duration": you enter a duration per day instead of start/end times, and the hours are centred around 12:00.
- A new **Full Day** period complements Morning / Break / Afternoon.
- Duration (hours) is visible and editable in the attendance list.
- Two-week calendars keep dedicated "Working Time 1st Week" / "2nd Week" lists, and switching between one-week and two-week mode rebuilds the lines automatically.
- Average hours per day and hours per week are derived from the same source and rounded to two decimals, so the displayed figures stay consistent.

## Technical data model changes

**resource.calendar.attendance**
- Removed fields: `date_from`, `date_to` (per-attendance validity dates) and `resource_id` (resource-specific attendance lines).
- `day_period` gains a `full_day` value; `duration_days` returns 1 for a full day.
- `duration_hours` is now stored and editable (`readonly=False`) with an inverse that writes `hour_from` / `hour_to`.
- New related field `duration_based`, new constraint `_check_day_period`: a Break (lunch) line is forbidden on a duration-based calendar.

**resource.calendar**
- New field `duration_based` (Boolean).
- `_check_attendance` / `_check_overlap` merged into a single `_check_attendance_ids` constraint, which now considers all non-section lines.
- Removed helpers: `_get_days_data`, `_get_resources_day_total`, `_get_max_number_of_hours`. The `Intervals` class moved to `odoo.tools`.
- `_get_hours_per_day()` no longer takes `attendances`; hours per day/week are computed only from global attendances.
- `_leave_intervals_batch()` loses its `any_calendar` argument.
- New helpers: `_get_two_weeks_attendance`, `switch_based_on_duration`, `_get_hours_for_date`, `_is_work_period`, plus flexible-resource work-interval methods (`_get_flexible_resource_valid_work_intervals`, `_get_flexible_resource_work_hours`, `_format_leave`, `_handle_flexible_leave_interval`).

**Other**
- `resource.resource.write(values)` renamed to `write(vals)`.
- `resource.calendar.leaves.default_get(fields_list)` renamed to `default_get(fields)`.

## How your habits should change

- **Dated attendance lines are gone.** If you used "Starting Date / End Date" on attendance rows to change a schedule over time, there is no equivalent: plan separate calendars (or a new calendar version) for those periods before upgrading.
- **No more resource-specific attendance lines.** Give the resource its own calendar instead.
- **Duration-based schedules cannot contain breaks.** Delete Break lines before switching a calendar to that mode.
- **Review customisations.** For OCA/custom modules, adapt calls to the renamed `write(vals)`, the removed helpers and the changed `_leave_intervals_batch` / `_get_hours_per_day` signatures before going live.

## What you gain by migrating

- One consistent model for working time: fully fixed or duration based, one or two weeks.
- Reliable hours-per-day, hours-per-week and work-time-rate figures, which feed payslips, leave and planning calculations.
- Clearer flexible-schedule support, including dedicated helpers for work intervals and leaves.
- Less field clutter and fewer overlapping constraints, making customisations simpler and future upgrades smoother.
- Staying on a maintained, supported version of Odoo.
