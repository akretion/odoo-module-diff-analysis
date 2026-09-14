# hr_holidays migration guide (18.0 -> 19.0)

## What's new for users

The official 19.0 release notes contain no entry about the Community `hr_holidays` module — the published extract covers localizations, accounting and payroll. The only Time Off item (India Flexi Leave with Optional Holidays) belongs to the Indian localization, outside a Community deployment, so it is not claimed here. All user-visible changes in 19.0 come from the refactors described below.

## Technical data model changes

**Contracts become employee versions.** `hr.contract` is merged into a new versioned employee model (`hr.version`), which `hr_holidays` now extends. Employee leave fields move from the deleted `hr.employee.base` abstract model to `hr.employee`, and a new `hr.employee.public` extension is added. Contract states (`open`, `close`, `kanban_state`) disappear, and payroll data now lives on the version: time-off code and salary rules that read contract fields must read version fields.

**Leave types.** `requires_allocation` and `employee_requests` switch from `'yes'/'no'` selections to Booleans (old domains such as `('requires_allocation', '=', 'yes')` no longer match). `show_on_dashboard` is replaced by `hide_on_dashboard`, with inverted meaning. Labels change: "No Validation" becomes "None needed", "Half Day" becomes "Half-Day", "Notified Time Off Officer" becomes "Notify HR".

**Leave requests.** `can_reset` and `action_reset_confirm` are removed; `leave_type_increases_duration` becomes an explanatory text instead of a Boolean. New fields appear: `max_leaves`, `virtual_remaining_leaves`, `dashboard_warning_message`, `can_validate`, `can_refuse`. `action_validate` becomes the private `_action_validate`, and automatic (no validation) leaves now go through `action_approve`.

**Employees.** `remaining_leaves` and `leaves_count` are removed. New computed fields (`leave_date_from/to`, `current_leave_state`, `allocation_display`, `is_absent`, `show_leaves`) drive the absence icons "On leave" / "Present but on leave".

**User preferences.** The related fields `leave_manager_id`, `show_leaves`, `is_absent`, `allocation_remaining_display`, `allocation_display` and `hr_icon_display` are removed from `res.users`: they can no longer be read or written from the user profile.

**Accrual plans.** Day parameters (`first_day`, `second_day`, `carryover_day`, `yearly_day`, ...) move from integers plus a display field to a single stored selection of days 1–31; `week_day` becomes 0–6 and month keys change from `jan`/`feb` to `1`/`2`. Existing data must be converted. Days are capped to the month length (31 February becomes 28/29), and 30 October is now selectable.

## How your habits should change

- **Approve vs Validate.** "Approve" only appears when a second approval is required; "Validate" appears when you have the rights to approve fully. With enough rights, a "both" request is validated in one click.
- **Overlaps.** Double bookings now show a warning message on the request form, and the same message blocks saving.
- **Deletion.** Only Time Off Administrators can delete approved requests; employees keep the right to cancel their own.
- **Leave types.** Settings are plain checkboxes: "Requires allocation", "Allow Employee Requests", "Hide On Dashboard".
- **Accrual plans.** Pick a day from the list instead of typing a number; the list follows the selected month.
- **Working schedule.** Changing an employee's calendar updates future time off, and is refused if allocations no longer cover it.
- **Profile.** Your Time Off approver is no longer editable from your own preferences; an HR user sets it on the employee form.

## What you gain by migrating

A single source of truth for HR data: employee history is versioned instead of duplicated between employee and contract, making past and future states auditable. Approvals become simpler and more predictable — the right button for the right person, clearer overlap warnings, stricter deletion rules for compliance. Accrual plans are easier to configure and no longer break on short months, and the employee and user screens are trimmed to what is actually used.
