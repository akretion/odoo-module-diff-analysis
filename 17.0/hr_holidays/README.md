# hr_holidays migration guide (16.0 -> 17.0)

## What's new for users

The provided Odoo 17 release notes extract only covers "Industries" and Odoo Experience topics — nothing about Time Off. The user-visible changes below therefore come from the hr_holidays commits themselves, and all apply to the Community edition.

- **See future allocations**: on allocation screens, pick a future date to see what accrual plans will grant, what will expire, and what is not yet available. The selector appears only for employees with accrual allocations.
- **Simpler allocations**: the "To Submit" and "Cancelled" states are gone; allocations are created directly in "To Approve".
- **Richer accrual plans**: choose credit at the *start* or *end* of the accrual period, a custom carry-over date (start of year, allocation date, or any month/day), an accrued-time cap, and a carry-over policy (nothing, everything, or a maximum).
- **Accrual simulation**: changing the start date or accrual plan instantly shows what the employee would receive — no more dummy allocations.
- **"Stress Days" renamed "Mandatory Days"** everywhere.
- **Accrual plans can be archived**, and concerned employees are listed from the plan form.
- **UX clean-up**: "Approval" menu renamed "Management"; "Current Year" filter replaced by "Currently Valid"; employee column removed from My Time Off lists; redundant dashboard legends removed.

## Technical data model changes

**Renamed model**: `hr.leave.stress.day` → `hr.leave.mandatory.day` (same fields). Requires a data migration.

**hr.leave**
- `holiday_allocation_id` removed: a leave is no longer attached to one specific allocation.
- `has_stress_day` → `has_mandatory_day`.
- Constraint `_check_holidays` replaced by `_check_validity`, based on `get_allocation_data`.
- New cron `_cancel_invalid_leaves`: leaves in the next 31 days that exceed accruals are cancelled automatically.

**hr.leave.allocation**
- Removed: `can_reset`, `taken_leave_ids`.
- `state`: `draft` and `cancel` removed, default is now `confirm`.
- Added: `already_accrued`, `has_accrual_plan`.
- `type_request_unit` is computed instead of related; `allocation_type` is now readonly.
- `_compute_leaves` no longer depends on taken leaves; new helpers `_get_carryover_date`, `_add_days_to_allocation`.

**hr.leave.accrual.plan**
- Added: `is_based_on_worked_time` (moved down from the level), `accrued_gain_time`, `carryover_date`, `carryover_day`, `carryover_day_display`, `carryover_month`, `added_value_type`, `active` (archive).
- Labels: "Level Transition" → "Milestone Transition"; levels are now "Milestone".

**hr.leave.accrual.level**
- Removed: `is_based_on_worked_time`, `parent_id` ("Previous Level").
- `added_value_type` is computed and its values became `day` / `hour` (was `days` / `hours`).
- `action_with_unused_accruals` options replaced: `postponed` → `all` / `maximum` / `lost`.
- `maximum_leave` is computed and gated by new `cap_accrued_time`; `added_value` defaults to 1; deleting a plan cascades to its levels.

**hr.employee**
- New `_get_consumed_leaves(leave_types, target_date, ignore_future)` returns per-allocation consumption and excess-day warnings.
- `_get_contextual_employee` is now `@api.model` and also reads `default_employee_id`.
- `get_stress_days*` renamed `get_mandatory_days*`.

## How your habits should change

- Allocations are no longer submitted: they are created straight in "To Approve".
- A leave is no longer tied to one allocation: consumption is re-matched across all valid allocations.
- Configure accruals at plan level (worked time, carry-over date, start/end crediting), not at level level.
- Old carry-over settings map as follows: "Transferred to the next year" → "All accrued time carried over"; "Lost" → "None. Accrued time reset to 0".
- Update procedures, reports and imports that mention stress days.
- Check future leaves regularly: they can now be cancelled automatically when accruals are insufficient.

## What you gain by migrating

- **Visibility**: employees and managers see future balances and upcoming expirations before they happen.
- **Better accrual rules**: custom carry-over dates, start/end crediting, caps and simulation.
- **Less rework**: fewer allocation states, clearer forms, auto-cancellation of over-accrued leaves.
- **Cleaner daily use**: menus, filters and reports that match real usage.
- **A maintained codebase**: this refactoring defines the 17.0 line; staying on 16.0 freezes behaviour.
