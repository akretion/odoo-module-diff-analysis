# hr_holidays migration guide (14.0 -> 15.0)

> The release notes extract supplied for this migration contains no section matching `hr_holidays`. Everything below is therefore derived from the code diff between 14.0 and 15.0 (Odoo PR #72511, "Improve Accruals, dashboards and general UX"), i.e. the Community addon as shipped.

## What's new for users

- **Accrual plans.** Accruals are no longer configured directly on an allocation. You now create a *Accrual Plan* (linked to a time off type) made of ordered *levels*. Each level defines a seniority threshold ("start after X days/months/years"), a gain (`added_value`, in days or hours), a frequency (daily, weekly, twice a month, monthly, twice a year, yearly), an optional "based on worked time" prorata, a maximum balance, and what happens to unused days at year end (postponed or lost). Level transitions can be immediate or effective after the current accrual period.
- **Simpler time off type setup.** The old "Mode" (`allocation_type`) and validity dates are gone. They are replaced by *Requires allocation* (Yes / No limit) and *Employee Requests* (extra days requests allowed or not), plus a colour and cover image used across screens.
- **Group requests in one record.** A leave or allocation can now target several employees at once (`employee_ids`); the record is split per employee when approved.
- **More counters and shortcuts.** Employee form shows a total number of allocations; time off types show an accrual count; leave requests expose a "Supporting Documents" action and a document counter.

## Technical data model changes

**New models:** `hr.leave.accrual.plan` (transition mode, levels, allocations, employees count) and `hr.leave.accrual.level` (sequence/level, start count & type, gain & type, frequency and its day/month fields, maximum leave, end-of-year action, SQL constraints).

**`hr.leave.type`:** removed `code`, `allocation_type`, `validity_start`, `validity_stop` and the `valid` field. Added `requires_allocation`, `employee_requests`, `color`, `icon_id`, `accruals_ids`, `accrual_count`; `valid` is replaced by `has_valid_allocation` (now allocation-based). `allocation_validation_type` selection changes from `hr/manager/both` (default `manager`) to `no/officer/set` (default `officer`).

**`hr.leave`:** removed `payslip_status`, the unused `cancel` state, `first_approver_id` and `second_approver_id`; added `employee_ids`, `multi_employee`, `holiday_allocation_id`, stored `employee_company_id`, `color`, `is_striked`, `supported_attachment_ids_count` and `action_documents()`. A new constraint forces a `holiday_allocation_id` when the type requires an allocation, and the validity check now uses the allocation's dates instead of the leave type's.

**`hr.leave.allocation`:** `date_from`/`date_to` become `Date` (were `Datetime`), `date_to` is no longer derived from the leave type; the `validate1` state is removed and the default state becomes `draft`. `first_approver_id` + `second_approver_id` merge into `approver_id`; `action_approve()` is dropped. Accrual fields (`accrual_limit`, `number_per_interval`, `interval_number`, `interval_unit`, `unit_per_interval`) are replaced by `accrual_plan_id`, `lastcall` and `nextcall`. Added `employee_ids`, `multi_employee`, `taken_leave_ids`, `is_officer`; `notes` becomes Text. Renamed helpers: `_holiday_status_id_domain` → `_domain_holiday_status_id`, `_prepare_holiday_values(employee)` → `(employees)`; `_update_accrual` is rewritten around plans/levels with a new `_end_of_year_accrual`.

**`hr.employee`:** new `allocations_count`; `_get_date_start_work` removed.

## How your habits should change

- Stop encoding validity periods on the time off type: set them on each allocation (start/end dates, now dates, not datetimes).
- Configure accruals through an accrual plan, not per allocation; an accrual allocation must reference a plan.
- Allocation approvals are a single step; the former second approval (`validate1`) no longer exists.
- New allocations start in Draft: confirm and approve them explicitly.
- To allocate or request for a group, select the employees on one record instead of creating one record per employee.
- Review each time off type: choose *Requires allocation* and *Employee Requests*, and re-check the allocation approval mode.

## What you gain by migrating

- Automated, rule-based accruals with seniority levels, prorata on worked time and year-end carry-over or loss policies.
- One clear place for validity and balances (the allocation), which removes a frequent source of "leave type not bookable" errors.
- Far less data entry for team-wide allocations and requests, with consistent per-employee records after approval.
- Clearer dashboards and counters on employees and time off types.
