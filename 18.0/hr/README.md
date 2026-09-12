# hr migration guide (17.0 -> 18.0)

This guide covers the Community addon `hr` (Employees app). Between 17.0 and 18.0 it got a UX refresh, more robust onboarding/offboarding and several data-model adjustments.

## What's new for users

- **Departments are now visible to every user.** Managers can consult information about their teams without being granted extra HR access rights. New actions let you jump from a department to its employees, or open its child departments straight from the kanban (plus new organization-chart filters).
- **Activities on departments:** a department can now carry scheduled activities and to-dos, like any other record.
- **Smarter activity plans (onboarding/offboarding):** when the responsible person (employee, manager or coach) is not linked to a user, Odoo walks up the management chain to find a user instead of failing. A circular reporting loop now raises a clear, explicit error message.
- **Employee form rework:** a new **Payroll** tab gathers payroll-related information, a new **Legal Name** field covers employees whose legal name differs from their preferred name (it is used in payroll and contract situations), fields are reordered, placeholders/tooltips and "no content" helpers were added, and buttons are hidden for archived employees.
- **Avatar card preview:** clicking an employee's or a resource's avatar (HR, Planning, Timesheets) opens a small card summarising their role, department and skills.
- **Related Contacts smart button:** a new counter on the employee form shows how many contact records are linked to the employee, with one-click access.
- **Smaller changes:** distance home/work can be captured with a unit of measure, badge/ID numbers are constrained to digits, the marital status list is built dynamically, and the presence-control settings were reworked (the Presence report is replaced by options on the Employees kanban).

## Technical data model changes

- **hr.department**: now inherits `mail.activity.mixin`; new methods `_search_has_read_access`, `action_employee_from_department`, `action_open_view_child_departments`.
- **hr.employee**: `_search()` no longer accepts an `access_rights_uid` argument; added computes/handlers for `km_home_work` (with inverse), related partners count, dynamic marital status selection, plus `action_related_contacts`, `_verify_barcode`, `get_views`, `check_field_access_rights`, `_employee_attendance_intervals`, `_get_expected_attendances`.
- **hr.employee.base**: new related fields `phone`, `email`, `share`, `im_status` (from the linked user).
- **resource.resource**: new related fields to the employee (`job_title`, `department_id`, `work_email`, `work_phone`, HR presence icon) and `_compute_avatar_128`; `employee_id` no longer filters out archived employees.
- **hr.job**: `copy()` replaced by `copy_data()` (batch copying).
- **mail.activity.plan**: `_compute_department_assignable`; **mail.activity.plan.template**: `_get_closest_parent_user`.
- **res.partner**: `_get_all_addr`.
- **Removed:** `hr_notify_user_ids` on the company and in Settings — the 17.0 feature that notified selected users when employee data changed was reverted.
- **Label change:** "Bank Account Number" is now simply "Bank Account".

## How your habits should change

- If you relied on the 17.0 *"notify these users when employee data changes"* setting, it no longer exists: use chatter followers or activity plans instead.
- The Presence report is gone; use the presence options on the Employees kanban.
- Onboarding/offboarding plans no longer block when a manager or coach has no user: the activity may be assigned higher up the management chain — check who actually receives it.
- Job positions are now copied in batches; custom code overriding `copy()` must move to `copy_data()`.
- Payroll information lives in its own tab, and payroll documents should be checked against the new Legal Name field.

## What you gain by migrating

- A clearer, faster Employee form (Payroll tab, legal names, tooltips, helpers) that reduces onboarding time for HR staff.
- Department-level visibility for every user, and team information for managers without loosening access rights.
- Onboarding and offboarding that keep working even with incomplete manager/coach/user mappings, with explicit errors instead of silent failures.
- Recognisable avatars and contact cards across HR, Planning and Timesheets, making resource picking easier.
- Cleaner data entry (numeric badge IDs, distance units, dynamic marital status) and a simplified configuration: fewer dead settings, fewer manual workarounds.
- A maintained codebase: core APIs (`_search`, `copy_data`) aligned with Odoo 18, so Community/OCA customisations built on `hr` stay upgradeable.
