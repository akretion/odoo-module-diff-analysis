# hr_work_entry migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes contain no extract matching this addon. The changes below are taken directly from the Odoo 20.0 source commits for `hr_work_entry`; only Community behaviour is described.

- The **Work Entry Type** form has been rebuilt around clear groups: computation, display, export codes, benefit eligibility and description. Tooltips and placeholders were added, and repeated labels (e.g. "Payroll Code" under "Exports Code") were removed.
- The work entry type list view gains new filters and hides irrelevant columns.
- The confusing pair of checkboxes "Is Time Off" / "Working Time" is replaced by a single **Category** selector — renamed **Count as** before the 20.0 release, with two values: *Working Time* and *Absence*.
- Work entry types are now offered per company, based on the company country.

## Technical data model changes

`hr.work.entry.type`
- Removed: `is_leave`, `is_work` (and the `_compute_is_work` / `_inverse_is_work` logic), `_is_unforeseen`, `_check_work_entry_type_country`.
- Added: `count_as` (Selection `working_time` / `absence`, required, default `working_time`). It was introduced mid-20.0 development as `category`, then renamed `count_as`.
- Added: `copy_data`, `_check_country_id`; `code` help text reworded.

`hr.work.entry`
- The model definition is no longer part of `hr_work_entry` (`models/hr_work_entry.py` removed); its fields, `create`/`write`/`unlink` overrides, conflict detection, `action_validate` and `action_split` moved out of this addon.

`hr.employee`
- Removed: `has_work_entries`, `action_open_work_entries`, `work_entry_source`, `work_entry_source_calendar_invalid`.
- Added: `external_code`.

`hr.version`
- Removed: `date_generated_from`, `date_generated_to`, `last_generation_date`, `work_entry_source`, `work_entry_source_calendar_invalid`, plus `_remove_work_entries`, `_cancel_work_entries`, the `write`/`unlink` overrides and the `_cron_generate_missing_work_entries` cron.
- `generate_work_entries(date_start, date_stop, force=False)` → `generate_work_entries(date_start, date_stop)` (the `force` argument is gone), on both `hr.employee` and `hr.version`. `_generate_work_entries` now returns a list of values instead of a recordset.
- New helpers for the reworked generation logic: `_get_real_attendances`, `_get_real_leaves_static`, `_get_real_leaves_static_attendance`, `_get_real_worked_leaves`, `_get_no_wet_or_wet_match`, `_get_work_entry_merge_key`, `_get_work_entry_source_fields`.

Other models
- `res.company`: `external_code` and computed `allowed_work_entry_type_ids`; `res.config.settings`: `external_code`.
- `resource.calendar.attendance`: `work_entry_type_id` is now stored-computed and auto-filled with the `WORK100` type matching the calendar country (`_default_work_entry_type_id` removed); computed `allowed_work_entry_type_ids`.
- `resource.calendar`: `_work_intervals_batch` accepts `resources_per_tz`; new `_compute_days_per_week`, `_compute_hours_per_day`, `_get_reference_hours_per_day`.
- `hr.employee.departure` override removed (warning on future validated work entries, `work_entries_warning_date`, `action_register`).

## How your habits should change

- Stop using "Is Time Off" / "Working Time": classify each work entry type with **Count as**.
- Work entry type codes are still referenced by salary rules — change them carefully.
- The work entry source per contract is no longer a visible selection on the employee record; attendance-based tracking is handled by the Attendance app as a boolean.
- No more "generated from/to" bookkeeping: you can no longer rely on stored last-generation dates to know what was already generated.
- Automatic monthly generation of missing work entries is no longer driven by this addon's cron; trigger generation from the payroll/work entry tools.
- Integrations calling `generate_work_entries(..., force=True)` must drop the `force` argument.

## What you gain by migrating

- One unambiguous classification field instead of two mirror checkboxes.
- A cleaner, self-explanatory work entry type form and list, with filters.
- Simpler generation logic with no stale generation-date ranges.
- Country-aware default work entry types on calendars and companies.
