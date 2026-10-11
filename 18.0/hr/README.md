# hr migration guide (17.0 -> 18.0)

## What's new for users

Odoo 18 does not add big new HR modules: it reworks the day-to-day UX of the Employees app and makes onboarding/offboarding flows more robust.

- **Onboarding & offboarding activities are more resilient.** When an activity plan must be assigned to a manager, a coach or the employee, and that person has no linked user account, Odoo now walks up the management chain until it finds a manager who has a user. A circular reporting loop raises an explicit message instead of failing silently.
- **New "Payroll" tab** on the employee form, gathering all payroll-related information in one place.
- **New "Legal Name" field**, for employees whose legal name differs from their usual/preferred name. It replaces "Name" in several payroll and contract situations.
- **Avatar card preview.** Clicking an employee (or resource) avatar opens a small card with role, department and skills, plus a "Send Message" button when a user is linked. Useful in Planning, Timesheets and HR.
- **Activities on departments** are now possible.
- **Cleaner employee form:** reordered fields, better placeholders and tooltips, clearer wording, "no content" helpers, improved graph/pivot measures, and action buttons hidden when the employee is archived.
- **Menu visibility:** the Department menu filter for non-department managers was adjusted (only the kanban entry is filtered out now).

## Technical data model changes

From the 17.0 → 18.0 code diffs:

- `hr.department` now inherits `mail.activity.mixin` in addition to `mail.thread`, which is what enables activities on departments.
- `hr.employee` gains new fields: `legal_name`; `phone`, `email` and `im_status` (related to the linked user); `share` (related to `user_id.share`). A new Payroll tab groups payroll fields in the form view.
- Field label change: `bank_account_id` is now labelled **"Bank Account"** instead of "Bank Account Number".
- `employee_type` help text rewritten: only Employees, Students and Trainee are considered to have a contract history.
- `resource.resource` (the record behind employees in Planning/Timesheets): `employee_id` is now read with `active_test=False` (archived employees are visible), and new related fields were added (`job_title`, `department_id`, `work_email`, `work_phone`, `show_hr_icon_display`, `hr_icon_display`). `avatar_128` is now computed from the linked employee, using `hr.employee.public` for users outside the HR user group.
- Removed fields: `res.company.hr_notify_user_ids` and `res.config.settings.hr_notify_user_ids`. The "notify these users when employee data changes" feature was reverted; existing data in these fields is dropped.
- Method change: the HR override of `mail.activity.plan.template._determine_responsible()` now returns a `{'error', 'responsible'}` dictionary and delegates to the new `_get_closest_parent_user(employee, responsible, error_message)` helper.

## How your habits should change

- **Activity plans:** you no longer have to make sure every manager or coach has an Odoo user. Check your reporting structure instead: a manager without user is now skipped in favour of their own manager. Fix circular reporting lines, which now block plan creation with a clear message.
- **Employee identity:** enter the legal name in the dedicated field and keep the working/preferred name as before. Contract and payroll documents rely on the legal name.
- **Employee type:** the help text is more explicit — check it if you used types to drive contract history.
- **Notifications on employee data changes:** the related setting is gone from Settings; no user will be notified through that mechanism anymore.
- **Labels and layout:** the bank account label changed, and several fields moved (notably into the Payroll tab), so update your internal procedures, saved filters and any custom views that referenced these fields.
- **Avatars:** clicking an avatar now opens a card instead of a plain link; this also changes how employee avatars are exposed to non-HR users.

## What you gain by migrating

- **Fewer blocked HR processes:** activity plans run even when managers or coaches have no user account, with clear errors when the reporting line is broken.
- **Better payroll and contract data quality**, thanks to the Legal Name field and the dedicated Payroll tab.
- **A less intimidating Employees app** for occasional users: clearer fields, tooltips, helpers and hidden actions on archived records.
- **Faster collaboration** in Planning, Timesheets and HR through avatar cards and department activities.
- **Cleaner permissions:** no more orphan notification settings, and employee avatars are shown through the public employee model to users outside the HR group.

