# hr_holidays migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes extract contains nothing specific to this addon. The points below come from the code changes actually shipped in `hr_holidays`.

- **Leave types are now "work entry types".** Time off requests, allocations and the dashboard reference `hr.work.entry.type` instead of `hr.leave.type`.
- **Simpler allocations.** The "Allocation Type" (Regular / Accrual) selector is gone: setting an accrual plan makes the allocation accrue, leaving it empty keeps it a fixed allocation.
- **More accurate accruals.** Days now accrue from the first day of an accrual level (the level's start day used to be skipped), the first carry-over date is no longer missed when saving an allocation, and the future balance shown on the dashboard matches what the daily cron accrues.
- **Durations editable in days or hours.** "Duration (days)" and "Duration (hours)" can be entered directly; the other field follows.
- **Country-safe leave types.** Only types matching the company country (plus generic ones) are selectable, with the country in the label: *Paid Time Off (Belgium)*, *Sick (Generic)*.
- **Simpler employee departure (Belgian payroll).** The "Cancel Time Off Requests" option is removed: future requests are always cancelled and allocations closed at the departure date.

## Technical data model changes

Removed fields:
- `hr.leave`: `request_unit_half`, `request_unit_hours`, `work_entry_type_filter_domain`.
- `hr.leave.allocation`: `allocation_type`, `already_accrued`, `yearly_accrued_amount`, `expiring_carryover_days`, `last_executed_carryover_date`.
- `hr.leave.accrual.plan`: `time_off_type_id`; `hr.leave.type`: `accruals_ids`, `accrual_count`, `action_see_accrual_plans`.
- `hr.employee` / `hr.employee.departure` / `hr.version`: `do_cancel_time_off_requests`, `departure_do_cancel_time_off_requests`.

Renames:
- `holiday_status_id` -> `work_entry_type_id` (`hr.leave`, `hr.leave.allocation`); `current_leave_id` -> `current_work_entry_type_id`; `leave_type_request_unit` -> `work_entry_type_request_unit`.
- Accrual allocations: `lastcall` -> `last_accrual`, `actual_lastcall` -> `lastcall`, `postpone_max_days` -> `max_carriedover_duration` (days *or* hours), `expiring_carryover_days` -> `previous_carryover_number_of_days`, `yearly_accrued_amount` -> `yearly_accrued_days`.
- `hr.leave.type.time_type` -> `hr.work.entry.type.count_as`.

Behaviour and signatures:
- `accrual_plan_id` is now a plain stored Many2one; accrual logic keys off its presence.
- `number_of_days` is no longer computed; `number_of_days_display` / `number_of_hours_display` gained inverses and write it.
- `_process_accrual_plans` now returns accrual values for a target date instead of writing on the allocation; `_get_consumed_leaves` takes `precomputed_allocations`; `_get_carryover_date` became `_get_next_carryover_date(date_from, date_from_included=True)`; `_add_days_to_allocation` and the level-lookup helpers were reworked.
- `allowed_work_entry_type_ids` (computed Many2many) restricts leave/allocation type domains; display names append the country.
- `resource.py` was split into `resource_resource.py`, `resource_calendar.py`, `resource_calendar_leaves.py` (no functional impact).

## How your habits should change

- Expect the new wording: "Time Off Type" now means work entry type, with a country suffix in dropdowns.
- When creating an allocation, don't look for an Allocation Type — pick an accrual plan for accruals, nothing for a fixed allocation.
- Enter the duration in the unit you think in (days or hours); the counterpart is computed.
- Filters, saved views, automated actions, Studio fields and custom reports using `holiday_status_id`, `allocation_type` or `time_off_type_id` must be rewritten — budget time for it before go-live.
- Belgian payroll customers have nothing to configure at departure anymore.
- Accrual balances still come from the accrual plan and its cron; don't correct `number_of_days` by hand.

## What you gain by migrating

- One single concept (work entry type) shared by leaves, allocations and payroll entries: less duplication, fewer inconsistencies.
- Trustworthy accrual maths — first-day accrual, correct first carry-over, dashboard forecast aligned with the cron — and fewer manual corrections.
- Country-scoped time off types: much lower risk of picking a type belonging to another country in multi-country setups.
- Faster daily work: no allocation-type step, durations typed in the right unit, and a departure process that runs automatically.
