# hr_holidays migration guide (12.0 -> 13.0)

This addon handles time-off requests, allocations and leave types in the Community edition. Between Odoo 12.0 and 13.0 it was reworked twice for a "back to basics" UX and access-right cleanup.

## What's new for users

No official release-note extract was available for `hr_holidays`: the notes shipped with this migration only cover Enterprise payroll/leave features that are **not** part of this Community addon, so they are not repeated here. The user-visible changes below are taken from the actual 12.0 → 13.0 code differences.

* **Simpler approval roles.** The "Time Off – Team Leader" group disappears. Three roles remain: Internal User, Time Off – All Approver (officer) and Time Off – Administrator.
* **One responsible per employee.** The "Time Off Responsible" field on the employee becomes the reference for first-level approval, and is pre-filled with the employee's manager.
* **Employee self-service.** Employees now see their own leave data (responsible, absence status, allocated days, days used, leave end date) in their Preferences, without gaining extra rights on other employees.
* **Notifications.** The employee is notified when a leave is approved or refused, with the leave type and date.
* **Mass actions.** Approvals and refusals can be performed in bulk from the approver menus.
* **Smarter leave-type ordering.** Types with a fixed allocation and remaining days come first, then free allocations, then already-taken types.
* **New totals.** Allocated days and used days are visible per employee.

## Technical data model changes

`hr.employee`
* `is_absent_totay` (typo) removed → replaced by `is_absent`, computed by `_compute_leave_status`.
* Added computed floats `allocation_count` and `allocation_used_count`.
* `leave_manager_id`: help text changed, domain now built by `_group_hr_user_domain`.

`res.users`
* New related fields: `leave_manager_id`, `show_leaves`, `allocation_count`, `allocation_used_count`, `leave_date_to`, `is_absent`, exposed through `SELF_READABLE_FIELDS`.

`hr.leave`
* `manager_id`: was `Many2one('hr.employee', string='Manager', readonly=True)`, now a plain `Many2one('hr.employee')` (no label, not read-only).
* `out_of_office_message` (Chat Status) **removed**; the chat out-of-office status is gone, and `models/mail_channel.py` was deleted.
* `state` no longer defaults to `confirm`; it is derived from the leave type validation setting.
* New `_check_double_validation_rules`; `_get_responsible_for_approval` now relies on `leave_manager_id` and the type's responsible.
* Approval/refusal now posts a notification to the employee.

`hr.leave.allocation`
* Boolean `accrual` replaced by selection `allocation_type` (`regular` / `accrual`).

`hr.leave.type`
* `virtual_remaining_leaves` becomes searchable (`_search_virtual_remaining_leaves`).
* `_order = "sequence, id"` replaced by `_model_sorting_key`.

No method signature changes were detected for this addon.

## How your habits should change

* Stop relying on the "Team Leader" group: grant **Time Off – All Approver** or **Time Off – Administrator** instead.
* Always set the **Time Off Responsible** on each employee; first approval now depends on it rather than on department managers.
* When configuring accrual allocations, use the new **Allocation Type** selector instead of the old "Accrual" checkbox.
* Do not expect the chat out-of-office message any more.
* Expect leave-type lists to be sorted/filtered by remaining days rather than by sequence.
* Encourage employees to check and maintain their own data from their Profile/Preferences.

## What you gain by migrating

* A clearer, auditable approval chain: one responsible, three explicit roles.
* Less administration: no per-team group maintenance, automatic pre-filling of the responsible.
* Employee self-service on personal leave data, with no loosening of `hr.employee` access rights.
* Better communication: automatic approval/refusal notifications.
* Bulk approval for managers and officers.
* Smarter, availability-aware leave-type ordering, and searchable "virtual remaining" amounts for reporting and filters.
