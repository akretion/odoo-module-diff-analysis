# hr_work_entry migration guide (18.0 -> 19.0)

## What's new for users

- **Work entries are now a date + a duration.** Per the release notes: "Work entries are now composed of a duration and a date instead of a start and end datetime." The "From"/"To" datetime pair is gone.
- **A reworked Work Entries widget** makes manual creation much easier (release notes: "The Work entries widget has been reworked to support easier manual creation").
- **Employees and contracts are merged** into one versioned employee record (`hr.version`). Work entries are attached to an employee *version* instead of a separate contract.
- **Attendance overtime can feed work entries**: with overtime rulesets configured, extra hours coming from Attendances can be turned into work entries when the contract source is set accordingly.
- **Richer leaves** (simultaneous leaves, non-uniform durations such as 1.5 days) are handled by the leave machinery that work entry generation relies on.

## Technical data model changes

`hr.work.entry` (the heart of this addon):

- Removed: `date_start` (Datetime, required), `date_stop` (computed Datetime), and the methods `_compute_date_stop()`, `_compute_duration()`, `_get_duration_batch()`.
- Added: `date` (Date, required) and `duration` (Float, default 8, stored and editable — no longer computed from the period).
- `_order` changed from `conflict desc,state,date_start` to `conflict desc,state,date`.
- Removed constraints: "work entry must end", "start before end", and the PostgreSQL EXCLUDE constraint forbidding overlapping validated entries. Removed index on `(date_start, date_stop)`; new index `(version_id, date)` filtered on draft/validated states.
- Conflict detection rewritten: an entry becomes `conflict` when an employee's **total duration for the same day exceeds 24 hours**, instead of when time ranges overlap.
- New helpers: `_to_intervals()`, `_from_intervals()`, `_onchange_version_id()`, `_mark_leaves_outside_schedule()`, `_get_leaves_entries_outside_schedule()`, `action_split(vals)`, `get_unusual_days()`, `_compute_display_name()`.
- `_set_current_contract()` resolves the version from a single `date`; error messages were adapted ("... has no contract on <date>").

Work entry generation now lives on `hr.version` (contract merging):

- `generate_work_entries()`, `_generate_work_entries()`, `_recompute_work_entries()`, `_remove_work_entries()`, `_cancel_work_entries()`, `has_static_work_entries()`, `_get_work_entries_values()`.
- New `_generate_work_entries_postprocess()` converts legacy `date_start`/`date_stop` into `date` + `duration`, splits entries spanning several local days (timezone aware) and regroups same-type entries of the same day into one line.
- Additional hooks: `_get_attendance_intervals()`, `_get_lunch_intervals()`, `_get_leave_work_entry_type()`, `_get_real_attendance_work_entry_vals()`, `_get_default_work_entry_type_overtime_id()`, `_get_bypassing_work_entry_type_codes()`, `_get_fields_that_recompute_we()`.
- `hr.employee.generate_work_entries()` added. `hr.work.entry.type`: `_compute_is_work()` / `_inverse_is_work()`. `resource.calendar`: `_compute_hours_per_week()`, `_get_global_attendances()`; `resource.calendar.attendance`: `_is_work_period()`.

## How your habits should change

- Enter a **date and a number of hours**; stop thinking in "From"/"To" times.
- A day may hold several entries (e.g. 7h + 1h) as long as the daily total stays within 24 hours.
- Overlap errors disappear — but a day exceeding 24 hours now raises a conflict.
- Entries are automatically merged per employee, per day and per work entry type, so lists are shorter and clearer.
- After migration, historical entries are rewritten on the new date + duration basis; a shift crossing midnight is split across the two local days (timezone of the calendar, employee or company).

## What you gain by migrating

- A single, simpler input model (date + duration) that matches how payroll consumes work entries.
- Faster manual creation and correction thanks to the reworked widget and the new split action.
- A predictable conflict rule based on daily volume rather than on hard-to-read time overlaps.
- Timezone-safe day splitting and automatic regrouping: fewer lines, less manual cleanup.
- Full alignment with the merged employee/contract versioning model (`hr.version`), the foundation of all 19.0 HR flows.
