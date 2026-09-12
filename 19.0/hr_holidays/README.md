# hr_holidays migration guide (18.0 -> 19.0)

## What's new for users
The official 19.0 release notes contain almost no core Time Off items. The only Time Off-related note is for the Indian localization: Flexi Leave is now fully supported with Optional Holidays, so employees using that leave type can only select eligible optional holiday days. No other Time Off feature is announced. The changes described below therefore come from the technical diff.

## Technical data model changes
- **Employee/contract merge**: Odoo 19 introduces `hr.version`, a versioned employee model replacing `hr.contract`. Payroll-impacting fields now live on `hr.version`, inherited by `hr.employee`. Time Off reads the working calendar from the employee version. Contract states (`open`, `close`, `kanban_state`) are removed. Salary rules now receive `version` in the localdict instead of `contract`.
- **Models/files**: `hr.employee.base` is removed; its fields move into `hr.employee`. A new `hr.employee.public` extension and `hr_version.py` are added.
- **`hr.employee`**: `leave_manager_id`, `current_leave_state`, `leave_date_from`, `leave_date_to`, `allocation_count`, `allocations_count`, `show_leaves`, `is_absent`, `allocation_display`, `allocation_remaining_display` and `hr_icon_display` are now defined directly on `hr.employee`.
- **Removed employee fields**: `remaining_leaves` and `leaves_count` (unused, buggy compute).
- **`hr.leave`**: removed `can_reset`. Added `can_approve`, `can_validate`, `can_refuse`, `can_cancel`, `holiday_status_requires_allocation`, `max_leaves`, `virtual_remaining_leaves`, `dashboard_warning_message`. `leave_type_increases_duration` changed from Boolean to Char (carries an explanatory message). Labels: `request_unit_half` "Half Day" -> "Half-Day"; `request_unit_hours` "Custom Hours" -> "Specific Time"; `duration_display` "Requested (Days/Hours)" -> "Requested".
- **Methods**: `action_reset_confirm` removed. `action_validate` becomes private `_action_validate`. `action_approve` is now the only public approve/validate entry point and chooses first vs final approval. `_action_user_cancel` and `_force_cancel` accept `reason=None`.
- **`hr.leave.type`**: `show_on_dashboard` inverted/renamed to `hide_on_dashboard`. `requires_allocation` and `employee_requests` changed from Selection to Boolean. Labels: "No Validation" -> "None needed"; "Notified Time Off Officer" -> "Notify HR"; "Public Holiday Included" -> "Ignore Public Holidays".
- **Accrual plan/level**: `carryover_day`, `first_day`, `second_day`, `first_month_day`, `second_month_day`, `yearly_day` changed from Integer to Selection (1-31). Months/weekdays now use numeric strings (`1`-`12`, `0`-`6`) instead of `jan`/`mon`. Day pickers show only days available in the selected month, and the "last day" value is gone. The SQL date constraint is replaced by a Python constraint.
- **`hr.leave.allocation`**: carryover/settlement date now uses `monthrange`, avoiding invalid dates such as Feb 31.
- **`res.users`**: removed related fields `leave_manager_id`, `show_leaves`, `is_absent`, `allocation_remaining_display`, `allocation_display`, `hr_icon_display`; also removed from self-readable/self-writable fields. `leave_date_to` remains.
- **Conflict check**: overlapping leaves now build a `dashboard_warning_message`; saving still raises it as a validation error.

## How your habits should change
The release notes announce no core Time Off habit changes for 19.0. The only behavior note is India-specific: Flexi Leave now restricts selection to eligible optional holidays. Everything else follows the data model changes above; expect renamed fields, the new approve/validate buttons, and numeric day/month pickers in accrual plans.

## What you gain by migrating
- One versioned employee model replaces separate contracts, so HR, payroll and Time Off share a single timeline with historical versions.
- Cleaner approval flow: one approve action, separate approve/validate/refuse buttons, and automatic validation uses the same path.
- Better overlapping-leave handling: conflicts are explained in a dashboard warning before saving.
- More accurate accruals: day pickers accept real calendar days, adapt to the selected month, and avoid the old "last day" shortcut. Carryover dates are computed safely.
- Simpler leave type setup with boolean toggles for "Requires allocation", "Allow Employee Requests" and "Hide On Dashboard".
- Removal of unused employee balance fields reduces confusion on employee forms.
- Ready for payroll localization updates, since salary rules now read the employee `version`.
