# hr_work_entry migration guide (19.0 -> 20.0)

Odoo 20.0 rebuilds how working time is produced and classified. The addon still generates the working time an employee is expected to do, but the way types, tracking methods and overtime are modelled changes substantially.

## What's new for users

- **Work entry types and time off types converge.** The plain "Is Time Off" / "Working Time" flags are gone; each type now carries a single **Category**: *Working Time* or *Absence*.
- **Time Rules (configurable overtime rules).** A new rule engine lets you express, per company/country and per employee domain: "when an employee works more (or less) than X hours per day or per week, reclassify the excess (or deficit) as type Z". Rules can be restricted to selected work entry types, to given weekdays, to public holidays and to a time window, with employee/employer tolerances and a salary rate. Rules run **in sequence**: each rule sees the output of the previous ones, so layered rules (excess > 2 h -> Overtime, excess > 4 h -> Double time) compose.
- **Compensatory leave.** Rule output can grant (excess) or deduct (deficit) time-off allocation credits, with a per-record log so a modification or deletion of the source record reverses the credit precisely.
- **Public holidays out of the box.** The Time Off release notes announce a "Public Holiday" time type available by default for all localizations, holidays loadable for the year with the company's country suggested.
- **Calendar days vs working days.** Time off can now be deducted in working days or calendar days, which changes the absences fed into the schedule.
- **Working schedules.** Working schedules can describe variable work periods, and can define a time per day without start/end times. Overtime analysis (which rule applied to which overtime) is also announced in the Attendances release notes, alongside overtime rulesets with public-holiday options.
- Nothing in the release notes covers the Community-only internals below; they come from the code changes.

## Technical data model changes

**hr.work.entry.type**
- Removed: `is_leave`, `is_work` (and `_compute_is_work` / `_inverse_is_work`), `_is_unforeseen`.
- Added: `category` (Selection: `working_time`, `absence`), required, default `working_time`.
- Renamed XML ids: `work_entry_type_attendance` -> `generic_work_entry_type_attendance`, `work_entry_type_leave` -> `generic_work_entry_type_leave`.

**hr.work.entry**
- `is_leave` (related) replaced by `category` (related).
- The model is no longer loaded by the addon (`models/__init__.py` no longer imports it); generation now returns value dictionaries instead of creating records.

**hr.version**
- Removed: `work_entry_source` (Selection "Tracking Method"), `work_entry_source_calendar_invalid`, `date_generated_from`, `date_generated_to`, `last_generation_date`.
- Added: `has_static_work_entries()` (returns True by default, overridden when attendance based), plus, in `hr_work_entry_attendance`, a Boolean `attendance_based` and a `_get_work_entry_source()` helper.
- `generate_work_entries(date_start, date_stop)` / `_generate_work_entries(...)`: the `force` argument is dropped, the result is a list of vals, and there is no more generation-window bookkeeping or nullification of older non-validated entries.

**hr.employee**
- Removed: `has_work_entries`, related `work_entry_source`, `work_entry_source_calendar_invalid`, `action_open_work_entries`, the custom `create_version`.
- Added: `external_code`. `generate_work_entries` loses `force`.

**hr.employee.departure** – the whole addon extension is removed (no departure warning on future validated work entries, no automatic reset to draft).

**res.company / res.config.settings** – `work_entry_source` ("Tracking Method" / "Default Tracking") removed.

**New models** – `hr.time.rule` and the abstract `hr.time.rule.source.mixin` (inherited by `hr.attendance` and `hr.leave`, firing the pipeline on validation); `hr.time.rule.allocation.log` on the leave side; an attendance validation policy per company. Work entry generation now tests `leave.count_as == 'absence'` instead of `leave.time_type == 'leave'`.

## How your habits should change

- Classify your work entry types by **Category** instead of ticking "Is Time Off"/"Working Time"; the old fields no longer exist, so saved views, filters, imports and custom code referencing them must be updated.
- The employee/contract **Tracking Method** and the company **Default Tracking** setting are gone. Attendance-based tracking is now decided by the Attendance app on the contract.
- There is no **Force** regeneration any more: regenerating no longer wipes existing non-validated entries, and no "Generated From/To"/"Last Generation Date" is displayed on the contract.
