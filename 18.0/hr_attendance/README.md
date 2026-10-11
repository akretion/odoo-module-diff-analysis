# hr_attendance migration guide (17.0 -> 18.0)

Scope: the `hr_attendance` addon (Community edition), from 17.0 to 18.0.

## What's new for users

- **Extra hours validation.** You can now validate extra hours, fully or partially, before they are counted. Approving or refusing the extra hours of an attendance is now part of the flow.
- **Simpler extra-hours configuration.** The "Count Extra Hours" toggle and the "Extra Hours Starting Date" have disappeared: extra hours are tracked and displayed without being enabled for a company or from a given date.
- **Improved UX.** Several attendance screens and settings have been revamped in 18.0.
- The 18.0 release notes also mention reworked settings for automatic check-out and missing hours. That is a settings-side improvement of the Attendance app and does not change the data model described below.

Note: the 18.0 release notes are largely about Enterprise apps (Payroll, Planning, Sign, ...). Only the points above concern `hr_attendance` in Community.

## Technical data model changes

Removed (company configuration):
- `res.company.hr_attendance_overtime` ("Count Extra Hours")
- `res.company.overtime_start_date` ("Extra Hours Starting Date")
- Both were also removed from `res.config.settings`.

Added:
- `res.company.attendance_overtime_validation`: Selection `no_validation` (default) / `by_manager`, exposed on `res.config.settings`.
- `hr.attendance.overtime_status`: Selection `to_approve` / `approved` / `refused`, computed and stored, with tracking. It defaults to `to_approve` when the company validates "By Manager", otherwise to `approved`.
- `hr.attendance.validated_overtime_hours` ("Extra Hours"): computed, stored, writable and tracked. It mirrors `overtime_hours` while the status is not yet approved/refused (and always when the company does not validate); once decided it can be edited to keep only a partial value.
- New actions on `hr.attendance`: `action_approve_overtime()` and `action_refuse_overtime()`.

Changed behaviour:
- `_compute_overtime_hours` was rewritten and now also handles negative overtime (extra hours "consumed" by later attendances), so net extra hours can be negative.
- `hr.employee._compute_total_overtime` no longer depends on a company flag: it sums `validated_overtime_hours` of attendances with `overtime_status = approved`, plus manual overtime adjustments.
- The employee's extra-hours smart button now opens `hr.attendance` records ("Attendances This Month", approved extra hours only) instead of `hr.attendance.overtime`.
- Changing the overtime thresholds still triggers a recomputation; enabling/disabling overtime and start dates no longer exist.

## How your habits should change

- You no longer enable extra hours: they are always computed and shown. Check your tolerances before upgrading.
- There is no starting date any more. Where a start date was used to ignore old attendances, that filter is gone; be ready to see historical extra hours.
- Decide on the new company setting "Extra Hours Validation": with **No Validation**, extra hours count immediately (17.0-like behaviour); with **By Manager**, each attendance with extra hours is "To Approve" and does not count in totals until a manager approves it (or refuses it).
- Managers validate from the attendance views; approvals and refusals are tracked in the chatter.
- The employee form's extra-hours button now shows the underlying attendances, not the overtime records.

## What you gain by migrating

- Real control over extra hours: a manager validation workflow instead of silently recorded overtime.
- Cleaner, less error-prone configuration: no toggle, no start date, just tolerances and a validation mode.
- Correct totals: employee extra-hours totals only include validated hours, plus manual adjustments.
- Negative extra hours handled properly, so a deficit is no longer ignored.
- Full traceability: overtime status and validated hours are tracked, and the employee view drills down to the attendances.
- Stays on the supported Community edition and aligned with the standard/OCA roadmap.
