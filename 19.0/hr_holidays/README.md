# hr_holidays migration guide (18.0 -> 19.0)

## What's new for users

The 19.0 release notes say almost nothing about Time Off at Community level: the only related entry is the India localization, where *Flexi Leave* now works with Optional Holidays so employees can only select eligible days. Everything below comes from the code actually shipped in `hr_holidays`.

- **Time Off types are simpler to configure**: "Requires allocation" and "Allow Employee Requests" become checkboxes instead of Yes/No selections, and "Show on dashboard" becomes an inverted **Hide On Dashboard** toggle.
- **Clearer wording**: "No Validation" becomes "None needed", "Half Day" becomes "Half-Day", "Notified Time Off Officer" becomes "Notify HR", and "Public Holiday Included" becomes "Ignore Public Holidays" (behaviour unchanged).
- **Accrual plans are easier to build**: day pickers now offer every real day of the selected month instead of 1–28 plus "last", invalid dates (e.g. 31 February) can no longer be entered, and an Accrual UX rework adds milestone dates, carry-over options and hourly frequencies.
- **Administrators can edit a leave already in the Approval stage** and move it back to approval.
- **The Time Off dashboard is faster** (one batched request), and public employee records now show Time Off smart buttons plus an "open calendar" action.
- **Leave duration appears in avatar cards** (Discuss / HR), with multi-company information.

## Technical data model changes

- `hr.leave.type.requires_allocation`: Selection (`yes`/`no`) → **Boolean**, default True.
- `hr.leave.type.employee_requests`: Selection → **Boolean**, default False, relabelled "Allow Employee Requests".
- `hr.leave.type.show_on_dashboard` **removed**, replaced by `hide_on_dashboard` (Boolean, default False).
- Label changes on `leave_validation_type`, `allocation_validation_type`, `request_unit`, `include_public_holidays_in_duration`, `responsible_ids`.
- `hr.employee.base`: unused computed fields `remaining_leaves` and `leaves_count` **removed** (double inheritance of `hr.employee.base` also cleaned up).
- Accrual models: months and weekdays are now stored as numbers (`'1'`–`'12'`, weekdays `'0'` Monday → `'6'` Sunday) instead of `jan`/`mon`. `carryover_day`, `first_day`, `second_day`, `first_month_day`, `second_month_day`, `yearly_day` are now **Selection fields of strings `'1'`–`'31'`**; their `*_display` twin fields are gone. `carryover_month` defaults to the current month; a Python `_check_dates` replaces the SQL constraint.
- `hr.leave` / `hr.leave.allocation`: `action_validate` replaced by `_action_validate`; new state computes (`_compute_can_validate`, `_compute_can_refuse`, `_compute_can_back_to_approve`) and `action_back_to_approval`; `_force_cancel(reason=None)`, `_action_user_cancel(reason=None)` and `_split_leaves(..., split_date_to=False)` now accept omitted arguments.
- Removed methods (dead code or core refactors): `_check_mandatory_day`, `_compute_can_reset`, `action_reset_confirm`, `action_open_records`, `_notify_get_recipients_groups`, `_auto_init`, `_get_remaining_leaves`.
- New helpers: `hr.employee.get_time_off_dashboard_data`, `_get_hours_per_day`, `_get_first_working_interval`; `res.partner._compute_leave_date_to`; `resource.resource._format_leave` (flex resources).
- `hr.version` takes over leave split/overlap logic (`_check_overlapping_contract`, `_get_leaves`, `_refuse_leave`…), following the merge of contracts into the versioned employee model.
- `res.users`: related fields `leave_manager_id`, `show_leaves`, `is_absent`, `allocation_remaining_display`, `allocation_display`, `hr_icon_display` removed; `leave_manager_id` is no longer self-writeable.
- Framework-wide renames: `default_get(fields_list)` → `default_get(fields)`, `write(values)` → `write(vals)`.

## How your habits should change

- Review saved filters, automated actions and exports using `requires_allocation = 'yes'/'no'` or `show_on_dashboard`; use `requires_allocation = True/False` and `hide_on_dashboard = False` instead.
- Existing accrual plans keep working, but check plans that relied on "last day of month": days are now explicit (1–31) and are clamped to the selected month.
- Custom views, reports or templates referencing the removed `*_display` accrual fields, `remaining_leaves`, `leaves_count` or the user-level leave fields must be rewritten.
- Custom code or automations calling `action_validate()` must call `_action_validate()`; cancelling a leave no longer requires a reason.
- Approval flows are richer: a manager/admin can now edit an approved-stage leave and send it back to approval, so review your validation rules if you restricted this before.

## What you gain by migrating

