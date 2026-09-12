# hr_holidays migration guide (17.0 -> 18.0)

## What's new for users

- **Draft state removed.** A time off request no longer starts in "To Submit": it is created directly in "To Approve" (or "Approved" for types needing no validation), and cancelled leaves get a real "Cancelled" status instead of being archived.
- **Multi-employee leaves and allocations removed.** One record is always for one employee. Creating leaves or allocations for a department, a company or an employee tag now goes through a dedicated wizard that creates one record per employee.
- **Approve from the calendar.** Requests can be validated straight from the calendar popover, without opening the form.
- **Allocations.** Their description is auto-generated (time off type + duration) and stays editable; a custom description survives a type change. Pending allocation requests are summarized on the employee record with a shortcut to open them.
- **Custom hours.** "Hour from"/"Hour to" are now free decimal fields (e.g. 8.5) instead of a fixed 30-minute list, and reversed or out-of-range values are corrected automatically.
- **Time off types.** You can include/exclude public holidays on a given type (with an overlap warning), hide a type from employee dashboards, and get a warning when allocated days will soon expire without being carried over. Accrual plans accept a yearly maximum cap per milestone.

## Technical data model changes

Fields:
- `hr.leave`: `active` removed (cancellation is the new `state = 'cancel'` value). `parent_id`, `linked_request_ids`, `holiday_type`, `employee_ids`, `multi_employee`, `category_id`, `mode_company_id`, `all_employee_ids`, `is_user_only_responsible` and the `type_value` SQL constraint
