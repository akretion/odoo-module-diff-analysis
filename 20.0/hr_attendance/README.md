# hr_attendance migration guide (19.0 -> 20.0)

## What's new for users

- **Attendance status workflow.** Each attendance now has a status (Draft / Validated / Refused). Company policy decides whether check-ins are validated automatically, validated only when they match the expected hours within a tolerance, or require an Officer/Manager to validate them.
- **Time types on attendances.** Every attendance carries a Time Type (work entry type) and its colour, so calendar and Gantt views show at a glance what time was booked. Underworked, expected and overtime hours are colour coded.
- **Overtime is produced by time rules.** The old per-ruleset overtime lines are replaced by rules that reclassify hours above/below a threshold. Generated attendances stay linked to the attendance they came from, so you can see which rule produced which extra hours.
- **Automatic check-out time for flexible schedules.** Employees with no working hours set can define a specific automatic check-out time.
- **Single check-in mode.** Alongside automatic check-out, a single check-in system can be enabled, recording only the first check-in of the day during scheduled working hours.
- **Absence detection.** The daily cron creates a short technical attendance for employees who did not check in, so undertime rules can compute missing hours.
- **Time-based filters.** Attendance records can be filtered and grouped by working schedule and day.
- **Access control.** Users without Attendances access rights can no longer use the app.

Other 20.0 release-note items (biometric device integrations, check-in photo, ruleset smart button, public-holiday rulesets) are Enterprise or non-hr_attendance features and are not delivered by this Community addon. Also note the old "overtime ruleset" concept itself no longer exists.

## Technical data model changes

Removed models: `hr.attendance.overtime.line`, `hr.attendance.overtime.rule`, `hr.attendance.overtime.ruleset`.

Added: `hr.time.rule` (defined in `hr_work_entry`) and the abstract `hr.time.rule.source.mixin`, which `hr.attendance` now inherits in addition to `mail.thread`.

`hr.attendance` fields removed: `overtime_hours`, `overtime_status`, `validated_overtime_hours`, `expected_hours`, `linked_overtime_ids`.

`hr.attendance` fields added: `work_entry_type_id` (required, defaults to the company's attendance work entry type), `wet_display_code`, `wet_color`, `state` (draft/validated/refused), `time_rule_id`, `source_attendance_id`, `overtime_attendance_ids`, `source_stale`.

Company settings removed: overtime thresholds (company/employee), Display Extra Hours, Attendance Overtime Validation, related fields on employee version (`ruleset_id`, `has_ruleset_id`). Replaced by attendance validation policy, validation tolerance and attendance work entry type.

Behaviour and method changes:
- `create`/`write`/`unlink` no longer recompute overtime lines. `write` flags existing derived outputs with `source_stale` when `check_in`, `check_out`, `break_duration`, `employee_id` or `work_entry_type_id` change; deletion flags them too.
- A `skip_time_rules` context is used by crons and system writes to avoid recursion.
- New methods: `action_validate`, `action_refuse`, `action_mark_reviewed`, `_update_tolerance_state`, plus pipeline hooks `_apply_record_output`, `_get_time_rule_output_vals`, `_get_time_rule_remainder_vals`.
- Display name now shows time type + duration, with a ⚠ marker on stale outputs.
- New database index on `(check_in, check_out, employee_id)`.

## How your habits should change

- **Validate attendances.** With a validation policy enabled, new check-ins stay in Draft instead of being implicitly approved. Validate individually, in bulk, or let the tolerance rule do it.
- **Set the Time Type** when creating or correcting an attendance manually: it drives payroll work entries and view colours.
- **Configure overtime as time rules**, ordered by sequence, instead of rulesets. Each rule sees the already-reclassified output of the previous ones (e.g. >2h becomes Overtime, then >4h becomes Double-time).
- **Approve on the attendance, not on overtime lines.** The separate Approve/Refuse overtime buttons are gone.
- **Review flagged records.** Editing or deleting a source attendance no longer silently recomputes derived records: they are marked ⚠ and must be reviewed.
- **Weekly undertime rules only run from the weekly cron**; past corrections will not retroactively trigger them.
- **Re-check your company settings** for validation policy, tolerance and default attendance time type after the migration.

## What you gain by migrating

- A single, unified time-rule engine replaces three overtime models with less duplicated configuration.
