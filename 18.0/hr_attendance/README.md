# hr_attendance migration guide (17.0 -> 18.0)

What changes in the Attendance app when moving from Odoo 17.0 to 18.0 (Community edition).

## What's new for users

**Extra hours validation (new workflow).** Odoo 18 lets you validate extra hours, fully or partially, instead of accepting every computed value. Each attendance line now carries an approval status (To Approve / Approved / Refused) and a validated "Extra Hours" amount that a manager can adjust. Approving or refusing is a one-click action from the attendance list.

**Automatic check-out and absence detection.** Two scheduled actions are now provided: one automatically closes forgotten check-outs, the other detects absences.

**Improved UX.** The app gains an onboarding flow, a reworked kiosk entry point, and cleaner list/pivot views. Extra-hours reporting also gains an "Expected Hours" measure, so you can compare worked time against expected time directly in the pivot view.

Note: the release notes also mention a broader rework of presence control (presence report removal, options moved to the Employees Kanban). Those changes essentially belong to `hr_presence`; for this addon they only translate into a small internal install-time check, so do not expect a new presence UI here.

## Technical data model changes

Removed fields (company-level overtime activation):
- `res.company.hr_attendance_overtime` ("Count Extra Hours")
- `res.company.overtime_start_date` ("Extra Hours Starting Date")
- The same two fields are removed from `res.config.settings`.

Added fields:
- `hr.attendance.overtime_status`: Selection `to_approve` / `approved` / `refused`, computed, stored, tracked
- `hr.attendance.validated_overtime_hours`: Float "Extra Hours", computed, stored, editable, tracked
- `res.company.attendance_overtime_validation`: Selection `no_validation` (default) / `by_manager`, exposed in Settings

Changed behaviours:
- Overtime is now always computed. No company flag or start date limits the calculation anymore, and negative overtime is supported.
- `hr.employee.total_overtime` is now the sum of **approved** validated extra hours plus manual adjustments, instead of the sum of all overtime records. Approval therefore directly drives the employee's overtime balance.
- The employee overtime smart button now opens "Attendances This Month", filtered on approved status, instead of raw overtime records.
- Changing tolerance thresholds still triggers an overtime recomputation; toggling a flag or date no longer deletes overtime records.
- Kiosk: each company gets its own unique kiosk key, and the PWA manifest is generated on demand.

Method signature delta: 15 added, 1 removed, 0 modified.
Added on `hr.attendance`: `_compute_expected_hours`, `_compute_overtime_status`, `_compute_validated_overtime_hours`, `_cron_absence_detection`, `_cron_auto_check_out`, `_load_demo_data`, `_read_group_employee_id`, `action_approve_overtime`, `action_refuse_overtime`, `action_try_kiosk`, `get_kiosk_url`, `has_demo_data`.
Added on `res.company`: `_check_hr_presence_control`, `_default_company_token`, `_init_column`.
Removed on `res.company`: `_action_open_kiosk_mode`.

## How your habits should change

- Stop looking for "Count Extra Hours" and "Extra Hours Starting Date" in Settings: they no longer exist. Extra hours are tracked from day one, with no activation step and no historical start date to configure.
- Choose an "Extra Hours Validation" policy in Settings. With **By Manager**, hours stay "To Approve" until a manager approves or refuses them — unapproved hours never count in the employee's balance.
- Use the new "Extra Hours" field on an attendance to correct the validated amount without altering the raw worked hours.
- On the employee form, the former Overtime button now lists the month's attendances rather than the overtime records.

## What you gain by migrating

- Real control over extra hours: a documented approve/refuse trail, with change tracking, instead of trusting every computed value.
- Fewer payroll surprises: employee overtime balances only include approved hours, plus justified adjustments.
- Less manual cleanup: forgotten check-outs and absences are handled by scheduled actions.
- Better reporting: expected vs. worked hours in the pivot, and a per-company kiosk key that removes duplicate-key headaches.
- Community-ready: everything above ships in the standard `hr_attendance` module — no Enterprise contract required.
