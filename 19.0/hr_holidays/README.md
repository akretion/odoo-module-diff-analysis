# hr_holidays migration guide (18.0 -> 19.0)

## What's new for users

The 19.0 release notes barely mention Time Off: they are dominated by Accounting, Payroll and localizations. The only item relevant to this addon's ecosystem is:

- **India Time Off:** Flexi Leave is now fully supported along with Optional Holidays — employees can only select from eligible days.

Nothing else Time Off–related is advertised for the Community edition, so don't expect Enterprise payroll novelties here. The visible changes come from the time off data model rework described below.

## Technical data model changes

**Contracts merged into employee versions**
- `hr.contract` disappears and becomes `hr.version`, inherited by `hr.employee`. Time Off reads calendars from the employee version. `models/hr_version.py` is added, `hr_employee_public.py` gains leave fields, `hr_employee_base.py` is removed (content moved to `hr.employee`).
- `leave_date_from`, `allocation_count`, `allocations_count`, `allocation_remaining_display`, `current_leave_state` move to `hr.employee`, restricted to `hr.group_hr_user`.

**Removed fields**
- Employees: `remaining_leaves`, `leaves_count`.
- `hr.leave`: `can_reset`. `hr.leave.type`: `show_on_dashboard`.
- `res.users`: `leave_manager_id`, `show_leaves`, `is_absent`, `allocation_remaining_display`, `allocation_display`, `hr_icon_display` (also removed from SELF_READABLE/SELF_WRITEABLE_FIELDS).

**Field type and label changes**
- `hr.leave.type`: `requires_allocation` and `employee_requests` become Booleans (were yes/no); `show_on_dashboard` becomes `hide_on_dashboard` (inverted); "No Validation" renamed "None needed"; `responsible_ids` → "Notify HR"; `request_unit` "Half Day" → "Half-Day"; `include_public_holidays_in_duration` → "Ignore Public Holidays".
- `hr.leave`: `duration_display` → "Requested"; `request_unit_half` → "Half-Day", `request_unit_hours` → "Specific Time"; `leave_type_increases_duration` is now a Char explanatory message; new computed `max_leaves`, `virtual_remaining_leaves`, `holiday_status_requires_allocation`, `dashboard_warning_message`.
- Accrual plan/level: `carryover_day`, `first_day`, `second_day`, `first_month_day`, `second_month_day`, `yearly_day` become day selections (all `*_day_display` fields removed); `week_day` and month fields now store numeric strings.
- Approval flags: `can_reset` removed; `can_validate` and `can_refuse` added beside `can_approve`; `can_cancel` now derives from approval rights.

**Method changes**
- `action_validate()` → private `_action_validate()`; `action_reset_confirm()` removed; `action_approve()` handles both approval levels; `_action_user_cancel(reason=None)` and `_force_cancel(reason=None, ...)` no longer require a reason. The accrual-level SQL `CHECK` becomes a Python constraint.

## How your habits should change

- **Approvals:** harmonized "Approve" (first level) and "Validate" (final) buttons; a Time Off officer with the rights moves "To approve" straight to "Validated".
- **Reset is gone:** refused or cancelled requests can no longer be reset to confirmed — duplicate them instead.
- **Deletion:** validated requests are deletable only by Administrators, never in the past.
- **Conflicts** are warned about live in the request form instead of blocking only at save.
- **Leave types:** tick booleans ("Requires allocation", "Allow Employee Requests", "Hide on Dashboard") instead of yes/no choices.
- **Accrual plans:** day dropdowns only offer days existing in the chosen month (e.g. 30 October is now selectable).
- **Employees:** working schedules now live on employee versions — check the timeline after migration; changing a schedule still errors if future leaves exceed the allocation.
- **Your profile:** you can no longer set your own Time Off approver in Preferences; HR sets it on the employee.

## What you gain by migrating

- One versioned source of truth for contracts and working schedules, so time off, allocations and accruals stay coherent over time.
- A clearer approval flow with the right buttons for the right roles.
- Fewer configuration mistakes: booleans and explicit labels replace yes/no selections.
- More reliable accrual plans: month-aware days, no impossible dates such as 31 February.
- Fewer blocked saves, and an upgrade path that stays supported for years to come.
