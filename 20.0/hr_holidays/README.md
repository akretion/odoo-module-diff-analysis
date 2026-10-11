# hr_holidays migration guide (19.0 -> 20.0)

## What's new for users

- **Working days or calendar days.** The time off taken can now be deducted in working days or in calendar days, depending on the time type configuration.
- **Minimum request duration.** A minimum amount of time can be defined for a time-off request.
- **Overview shows everyone.** The Time Off overview now lists all employees, including those without an active request.
- **Access control.** A new "No" access right lets you prevent a user from using the Time Off app.
- **Public holidays out of the box.** The "Public Holiday" time type is now available by default in all localizations, and public holidays can be loaded for a whole year, with the ones matching the company's country suggested.

The release notes also cover Payroll and localization features that ship with Enterprise or country modules; they are out of scope for this addon.

## Technical data model changes

**Time off types become work entry types**
- `hr.leave.type` is removed; time offs and allocations now use `hr.work.entry.type`.
- Renamed: `holiday_status_id` → `work_entry_type_id`, `holiday_status_requires_allocation` → `work_entry_type_requires_allocation`, `leave_type_request_unit` → `work_entry_type_request_unit`, `leave_type_support_document` → `work_entry_type_support_document`, `leave_type_increases_duration` → `work_entry_type_increases_duration`.
- New `allowed_work_entry_type_ids` (leave and allocation) and `count_as` on the leave; `work_entry_type_filter_domain` removed.
- `hr.employee.current_leave_id` → `current_work_entry_type_id`.
- Types are filtered by country; their display name now shows the country (or "Generic").
- Obsolete `request_unit_half` / `request_unit_hours` removed.

**Allocations**
- `allocation_type` (Regular/Accrual) removed: a linked accrual plan now makes an allocation an accrual.
- `number_of_hours_display` renamed `number_of_hours`; balances are tracked in both days and hours (consumption keys are prefixed `days_`/`hours_`).
- `number_of_days` is no longer computed; the display fields now have inverse methods.

**Accruals**
- `hr.leave.accrual.plan`: `time_off_type_id` removed, `company_id` no longer computed; `accruals_ids`/`accrual_count` removed from the leave type. On accrual levels, `postpone_max_days` → `max_carriedover_duration` (unit follows `added_value_type`).
- Allocation fields: `lastcall` → `last_accrual`, `actual_lastcall` → `lastcall`, `yearly_accrued_amount` → `yearly_accrued_days`, `expiring_carryover_days` → `previous_carryover_number_of_days`; `already_accrued` and `last_executed_carryover_date` removed.
- Refactor: `_process_accrual_plans` now returns a dict instead of writing the allocation; `_get_carryover_date` → `_get_next_carryover_date(date_from, date_from_included=True)`; `_get_future_leaves_on` → `_get_additionnal_future_leaves_on`; `_get_consumed_leaves` gained a `precomputed_allocations` argument. The first carryover date is no longer skipped.

**Time rules (new)**
- `hr.time.rule` (plus `hr.time.rule.allocation.log`) reclassifies attendance or time off above/below a threshold and grants or deducts compensatory leave. `hr.leave` inherits `hr.time.rule.source.mixin` and gains `time_rule_id`, `source_leave_id`, `output_leave_ids`, `is_time_rule_trimmed`, `time_rule_time_range`.

**Other**
- Employee departure: `do_cancel_time_off_requests` removed, future requests are always cancelled.
- `resource.py` is split into `resource_calendar_leaves.py`, `resource_calendar.py` and `resource_resource.py`.

## How your habits should change

- Think "work entry type", not "time off type": requests and allocations use country-specific time types, shared with payroll.
- No more Regular/Accrual toggle: link an accrual plan, or leave it empty for a regular allocation.
- Accrual plans no longer reference a time off type, and the carryover cap is set in the accrual unit (days or hours) as "Max carriedover duration".
- Balances are stored in days and hours: enter the duration in the unit you use instead of relying on automatic conversion.
- A time off can no longer be duplicated.
- Leaves split or trimmed by a time rule show an HH:MM range and may display in hours even for day-based types.

## What you gain by migrating

- A rewritten accrual engine: no more skipped first carryover, days credited from the level's first day, far fewer bugs.
- Balances kept consistently in days and hours.
- Country-scoped time types, preventing wrong allocations and payroll impacts.
- Configurable time rules that turn overtime into compensatory leave automatically.
- Public holidays and richer overviews available out of the box.
