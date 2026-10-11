# hr_holidays migration guide (17.0 -> 18.0)

## What's new for users

**Multi-employee leaves and allocations have been reworked.** The way multi-employee leaves and allocations are created and managed changed. You can no longer create a single time off record covering a whole company, department or employee tag: the multi-employee wizard creates an individual leave per employee instead. This is actually a fix — one shared record could not handle employees with different working schedules, time zones or part-time contracts, so durations were wrong. Allocations keep their batch creation flow, still ending as one allocation per employee.

**Cancelled leaves are now a real status.** The old "archive" trick to hide a cancelled leave is gone. Cancelled time off now has its own **Cancelled** state, so your reports and dashboards count them properly and you can still trace what happened.

**Cleaner duration display.** The several duplicate duration fields shown on requests have been consolidated: the request now simply displays its duration in days or hours depending on the time off type.

**Custom hours are easier to enter.** The "Hour from"/"Hour to" fields on hour-based requests are now free numeric fields in HH:MM form instead of a long dropdown list of pre-set times.

**Allocation name is auto-generated.** Allocation descriptions are now generated automatically from the time off type and duration, and you can still overwrite them with your own text — your custom name is no longer reset when you change the allocation type.

## Technical data model changes

`hr.leave`:
- `employee_id` is no longer computed from `employee_ids`; it is now a plain, **required** field defaulting to the current user's employee.
- Removed: `parent_id`, `linked_request_ids`, `holiday_type`, `employee_ids`, `multi_employee`, `category_id`, `mode_company_id`, `all_employee_ids`.
- Removed the `type_value` SQL constraint; `_validate_leave_request`, `_compute_department_id`, `_compute_company_id`, `_compute_resource_calendar_id` now depend only on `employee_id`.
- `request_hour_from` / `request_hour_to` converted from Selection to Float.
- Removed duplicate duration fields `number_of_days_display`, `number_of_hours_display`, `number_of_hours_text`; `duration_display` is now stored.

`hr.leave.allocation`:
- `employee_id` required, defaults to the current user's employee.
- Removed: `parent_id`, `linked_request_ids`, `holiday_type`, `employee_ids`, `multi_employee`, `category_id`, `mode_company_id`, and its `type_value` constraint.
- `name` is now a stored, editable computed field (auto title + `is_name_custom` flag); `private_name` removed.

Also removed: the `active` fields on leaves and allocations (`state = 'cancel'` replaces archiving), `is_user_only_responsible`, and `_action_validate_create_childs` / `_prepare_holiday_values` on allocations. Group checks moved to `self.env.user.has_group(...)`.

## How your habits should change

- To give the same leave to several employees, use the multi-employee wizard: each employee gets their own request, and approvals are per leave.
- Batch allocations by department/company/tag stay available, but they no longer exist as one parent allocation record — expect one allocation line per employee.
- Stop archiving leaves to cancel them; use the cancel action and the new **Cancelled** status.
- Drop any custom fields, filters or report relying on `holiday_type`, `employee_ids`, `multi_employee`, `parent_id` or `private_name`; they disappear in 18.0. Review your Studio fields.
- If you used the "Hour from/to" dropdown values, reselect them: the new fields are numeric time inputs.

## What you gain by migrating

- Correct durations and approvals for part-time, multi-schedule or multi-timezone teams.
- Reliable time off reporting: cancelled requests keep a status instead of silently disappearing.
- Fewer confusing fields on forms, automatic allocation descriptions.
- Faster, friendlier entry of hour-based requests.
- A simpler data model, aligned with the standard Odoo 18 upgrade path and easier to maintain or extend.

For Enterprise-only additions mentioned in the release notes (accrual expiry and yearly caps, employee overview approvals, public holiday inclusion on time off types, dashboard visibility options, calendar working hours), confirm module availability before relying on them; they are not part of the Community addon changes described above.
