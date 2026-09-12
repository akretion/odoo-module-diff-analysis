# hr_holidays migration guide (17.0 -> 18.0)

## What's new for users

The headline change is the **removal of multi-employee time off**. In 17.0 a single request or allocation could target "By Company", "By Department" or "By Employee Tag", covering many employees at once. In 18.0 every request and allocation belongs to exactly one employee; batch creation is handled by dedicated wizards that generate one individual record per employee. This fixes a real problem: duration, calendar and time zone depend on each employee's own schedule, so a shared record could not compute correct durations (typically mixing a part-timer with a full-timer).

What you will see:
- The "Allocation Mode" selector (By Employee / Company / Department / Employee Tag) is gone from request and allocation forms.
- The Employees field, the multi-employee flag, the category and company-mode fields disappear from screens, filters and groupings.
- Cancelled leaves are no longer archived: a new **Cancelled** state keeps them visible in lists and reporting.

The remaining release-note bullets for Time Off (accrual expiration date, yearly accrual cap, working hours shown in the calendar, hours in reporting, approval straight from the overview, public holidays on time off types) are not reflected in this addon's data model and may come from Enterprise modules or other apps — check with your integrator whether they apply to your edition.

## Technical data model changes

Removed on `hr.leave`: `parent_id`, `linked_request_ids`, `holiday_type`, `employee_ids`, `multi_employee`, `category_id`, `mode_company_id`, `all_employee_ids`, the duration aliases `number_of_days_display`, `number_of_hours_display`, `number_of_hours_text`, `is_user_only_responsible` and `active`.

- `employee_id` moves from a computed field (derived from `employee_ids`) to a plain stored, required field, defaulting to the current user's employee. Same change on `hr.leave.allocation`.
- `hr.leave.state` gains `cancel` while `active` disappears; `_force_cancel()` now writes `state = 'cancel'` then calls `_post_leave_cancel()` (it used to archive the record), and `unlink()` reuses that helper. The `type_value` SQL constraint is dropped on both models.
- `hr.leave.allocation` also loses `private_name`; `name` becomes stored and editable while still auto-computed until the user changes it (`is_name_custom`, new `_get_title()`).
- `request_hour_from` / `request_hour_to` were half-hour Selection lists and are now Float fields (HH:MM), clamped by the new `_onchange_hours()`.
- Removed methods: `_prepare_employees_holiday_values`, `_prepare_holiday_values`, `_action_validate_create_childs`, `_get_employees_from_holiday_type`, `_compute_all_employees`, `_compute_from_holiday_type`, `_compute_from_employee_ids`.
- Depends on `_compute_department_id`, `_compute_company_id`, `_compute_resource_calendar_id` and `_compute_from_employee_id` are reduced to `employee_id`. Conflict checks, display names, approval rights and `_validate_leave_request()` now work on a single employee.

## How your habits should change

- Create one request per employee; for a team, use the new batch wizard instead of picking a department or a tag.
- Approve employee by employee: manager and officer checks no longer aggregate several people.
- Cancel through the Cancel action rather than archiving; cancelled requests stay in the history.
- Allocations remain per employee: batch allocations go through the wizard and produce one allocation per employee (and per accrual plan).
- Custom hours are free Float values now, no longer limited to half-hour drop-down choices.

## What you gain by migrating

- Correct durations: each employee's schedule, time zone and part-time ratio are respected, so a one-week leave is one week for everyone.
- Simpler screens: no mode selector, no half-working multi-employee records, fewer fields to explain to newcomers.
- Reliable history: cancelled leaves stay visible and reportable instead of being hidden by archiving.
- Predictable integrations: stored and required employee fields, and one duration concept (`number_of_days`, `number_of_hours`, `duration_display`).
- A supported upgrade: Odoo ships upgrade scripts that turn existing multi-employee records into individual ones.
