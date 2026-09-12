# hr_holidays migration guide (13.0 -> 14.0)

## What's new for users

- **Separate approvals for allocations and leaves.** In 13.0 a single setting drove both the allocation and the leave request of a Time Off Type. In 14.0 each type carries two independent settings: *Leave Validation* and *Allocation Validation*. Compensation days are the typical case: a manager approves the allocation, then reviews the leave requests separately.
- **Clearer labels.** Leave validation becomes "No Validation / By Time Off Officer / By Employee's Manager / By Employee's Manager and Time Off Officer". Allocation modes become "No Limit", "Allow Employees Requests", "Fixed by Time Off Officer".
- **Accrued time off** is more flexible: accruals can be defined on a day, week, month or year basis, and a start date can be set when time off is taken so that weekend/public holidays are handled correctly.
- **Time off expiration dates** are visible on the employee dashboard.
- The module now consistently speaks of a **Time Off Manager** instead of "Leave Manager" (fields, help texts and error messages).

Note: the other items in the release-notes "Employees" section (Planning availability, Discuss avatar chat, salary configurator, document apps) belong to other addons or to the Enterprise edition and are not part of this Community module.

## Technical data model changes

On `hr.leave.type`:
- `validation_type` was renamed to `leave_validation_type` (same keys, new labels, default `hr`).
- New `allocation_validation_type` selection (`hr`, `manager`, `both`; default `manager`), dedicated to allocations.
- `allocation_type` labels were rewritten (keys unchanged).

On `hr.leave` and `hr.leave.allocation`:
- The related `validation_type` field now reads `leave_validation_type` (leaves) or `allocation_validation_type` (allocations).
- Many onchange-driven fields became **stored, editable computed fields** (`compute=..., store=True, readonly=False`): `state`, `date_from`, `date_to`, `number_of_days`, `employee_id`, `holiday_status_id`, `manager_id`, `department_id`, `category_id`, `mode_company_id`, `request_unit_half/hours/custom`, plus the allocation fields `date_to`, `number_of_days`, `number_per_interval`, `interval_number`, `unit_per_interval`, `interval_unit`.
- The `_onchange_*` methods are replaced by `_compute_*` equivalents and `_default_employee` was removed, so any custom module overriding them must be adapted.
- Approval rights were adjusted: the second approval of an allocation is now checked against the Time Off Officer group (`is_officer`) rather than the manager group, and `_get_responsible_for_approval()` was rewritten around the new settings. `action_validate()` now also accepts types configured with "No Validation".
- A `leave_consolidated_date_to` field (employee/user) was added and then reverted during the same cycle: it is **not** part of final 14.0.

## How your habits should change

- Review every Time Off Type: leave validation and allocation validation are now configured separately.
- When employees may request allocations, select the responsible Time Off Officer(s) per type.
- Re-check accrual rules (unit and start date), as more combinations are now allowed.
- Expect "Time Off Manager" wording in menus, fields and error messages.
- Look at the employee dashboard for time off expiration dates instead of computing them elsewhere.

## What you gain by migrating

- Finer approval policies: allocations and leaves can follow different approvers without workarounds.
- Compensation days, banked hours and similar schemes become natively configurable.
- Self-explanatory labels for validations, allocation modes and error messages.
- More flexible accruals (day/week/month/year) and safer handling of weekends and public holidays when setting a time off start date.
- A more robust data model (stored computed fields) that behaves consistently for imports, RPC calls and integrations.
