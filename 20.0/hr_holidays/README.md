# hr_holidays migration guide (19.0 -> 20.0)

## What's new for users

No official 20.0 release-note extract matched this addon, so this section is derived from the 19.0 → 20.0 code changes only.

- **Allocations are simpler.** The "Allocation Type" selector (Regular / Accrual) is gone. An allocation with an accrual plan *is* an accrual allocation; an allocation without one is a regular allocation.
- **Accrual plans are no longer tied to a time off type.** The link was already unused in practice and has been removed, so accrual plans can be used with any time off type. The "Accruals count" figure and the "see accrual plans" button on the time off type form disappear.
- **Time types are now country-aware.** The "Time Type" (`work_entry_type_id`) picker only offers work entry types belonging to the company's/employee's country (or generic ones), and each entry now shows its country — or "(Generic)" — in its name. This prevents selecting a foreign type and skewing payroll or balances.
- **End-of-collaboration is automatic.** The departure wizard no longer asks which actions to run; cancelling future time off and closing running allocations happen automatically on registration. Only the application date remains configurable.
- **Half-day and hour-based requests** continue to work exactly as before, but they are now driven solely by the time type's request unit rather than by dedicated flags on the leave.

## Technical data model changes

Fields removed:
- `hr.leave`: `request_unit_half`, `request_unit_hours`.
- `hr.leave.allocation`: `allocation_type`. `accrual_plan_id` is now a plain stored/tracked Many2one (no compute, no inverse).
- `hr.leave.type`: `accruals_ids`, `accrual_count`, plus methods such as `_compute_accrual_count`, `get_allocation_data`, `action_see_accrual_plans`.
- `hr.leave.accrual.plan`: `time_off_type_id`; `company_id` is no longer computed from it.
- `hr.employee`, `hr.version`, `hr.employee.departure`: `departure_do_cancel_time_off_requests` / `do_cancel_time_off_requests`.

Fields added / moved:
- `allowed_work_entry_type_ids` on `hr.leave` and `hr.leave.allocation`, restricting `work_entry_type_id` by country; `work_entry_type_filter_domain` removed.
- Leave-related counters, searches and allocation helpers (`_compute_leaves`, `_compute_valid`, `get_allocation_data`, `has_accrual_allocation`, …) moved from `hr.leave.type` to `hr.work.entry.type`.
- `hr.employee`: `get_allocations_number`, `_get_hours_for_date`, `_get_leave_manager_domain`, `_compute_current_work_entry_type_id`; `_get_consumed_leaves` now takes `work_entry_types`.
- `resource.calendar.leaves`: `_cron_generate_public_holidays`, `load_public_holidays`, `_compute_company_id`; `company_id` is now required on `resource.calendar`.

Internal: `resource.py` was split into `resource_calendar.py`, `resource_resource.py` and `resource_calendar_leaves.py`. Method-signature delta: 7 modified, 129 added, 67 removed — largely the accrual-engine and work-entry refactors.

## How your habits should change

- Stop looking for "Allocation Type": use the presence of an accrual plan to tell accrual from regular allocations.
- Never try to restrict an accrual plan to one time off type — that configuration no longer exists.
- Set the company country (and employee company) correctly: it now drives which time types you can pick.
- On departure, no checkboxes to tick; only choose the departure date.
- Any custom field, filter, automation or report referencing the removed fields must be updated during migration.

## What you gain by migrating

- A leaner allocation model that is easier to explain and to audit.
- Country-safe time types, reducing payroll and balance errors.
- A rebuilt accrual engine with cleaner level, carry-over and expiration handling.
- Faster, fully automatic offboarding.
- A codebase aligned with the 20.0 framework, so future upgrades and customizations stay cheaper.
