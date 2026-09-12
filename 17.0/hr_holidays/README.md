# hr_holidays migration guide (16.0 -> 17.0)

This document summarizes what changes for the **Time Off** application (`hr_holidays`, Community edition) when moving from Odoo 16.0 to 17.0.

> **About the official 17.0 release notes:** the extract available for this addon covers only *Industries / data modules* and Odoo Experience videos — **nothing relevant to Time Off**. Everything described below therefore comes from the actual code changes shipped in 17.0, not from marketing material.

## What's new for users

**Stress Days are now called Mandatory Days.** The whole concept has been renamed in the menu, in the calendar configuration and in error messages ("You are not allowed to request a time off on a Mandatory Day").

**Cleaner menus and lists**
- The *Approval* menu is renamed **Management**.
- *My Allocations*: the "Employee" group-by is removed, and the `Current Year` filter is replaced by **Currently Valid**.
- *My Time Off*: the Employees column is removed from list and kanban views.

**Better time-off dashboard**
- Opening a time off from the dashboard now shows its state directly.
- The two legends *Public Holiday* and *Mandatory Day* are removed.

**Improved allocation request form (employee side)**
- The allocation name is now computed automatically (time-off type, duration and unit).
- The **Allocation Type** field is hidden when no active accrual plan exists in the database.

**Accrual plans**
- Plans can now be **archived** from the action menu.
- An **Employees** button is available on the plan form.

## Technical data model changes

**Renames / removals**
- Model `hr.leave.stress.day` → `hr.leave.mandatory.day` (data migration of existing records expected).
- `has_stress_day` → `has_mandatory_day`; `_compute_has_stress_day` → `_compute_has_mandatory_day`; `_check_stress_day` → `_check_mandatory_day`; `get_stress_days` / `_get_stress_days` → `get_mandatory_days` / `_get_mandatory_days` (and the related `get_*_days_data` helpers). No public method signature change beyond these renames.
- `hr.leave.accrual.level`: **`parent_id` (Previous Level) removed**; `is_based_on_worked_time` **moved to the plan**; `level_ids` is now labelled *Milestone* and `transition_mode` *Milestone Transition*.

**Accrual level behaviour**
- `added_value_type` values change from `days`/`hours` to `day`/`hour`, and the field is now computed (new `can_modify_value_type`); `added_value` defaults to 1.
- `maximum_leave` is now computed and driven by the new `cap_accrued_time` boolean (default: on).
- `action_with_unused_accruals` values change from `postponed`/`lost` to `lost` / `all` / `maximum`, with `all` as default; `postpone_max_days` semantics follow.

**Accrual plan (new fields)**
- `active` (archiving), `accrued_gain_time` (`start`/`end`, default `end`), `is_based_on_worked_time`, `added_value_type`, and a custom carry-over date: `carryover_date` (`year_start` / `allocation` / `other`), `carryover_month`, `carryover_day`.

**Allocation**
- `allocation_type` becomes read-only; `type_request_unit` is now computed instead of related; `number_of_days_display` becomes writable.
- New `has_accrual_plan` and `already_accrued` fields.
- New onchange simulation: changing `date_from` or `accrual_plan_id` previews the days the plan would grant.
- Internal method `_end_of_year_accrual` removed; `_update_accrual` simplified (year-end logic is now handled through the carry-over date).

A third change set (future-allocations refactoring, PR 108148) also lands in 17.0; it is not detailed here.

## How your habits should change

- Update your vocabulary and any saved filters/reports: **Stress Day → Mandatory Day**, **Approval → Management**, **Current Year → Currently Valid**.
- Existing mandatory-day records must be migrated to the new model; check your time-off calendar before go-live.
- **Re-review every accrual plan**: the previous `parent_id` level chaining is gone, and "At the end of the calendar year, unused accruals will be..." is replaced by a *Carry over* choice. Old behaviour ("Transferred to the next year") maps to *All accrued time carried over*; "Lost" maps to *None. Accrued time reset to 0*.
- Decide per plan whether time is granted **at the start** or **at the end** of the accrual period, and whether accrual follows worked time or calendar days.
- Set the carry-over date explicitly (year start, allocation date, or a custom day/month) — you are no longer forced onto 1 January.
- You no longer need to create "fake allocations" to see what a plan would give: open an accrual allocation and change the start date to simulate.

## What you gain by migrating

- A clearer, less cluttered Time Off UX for employees and managers (renamed menus, simplified list/kanban views, state visible from the dashboard).
