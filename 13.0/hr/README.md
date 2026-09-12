# hr migration guide (12.0 -> 13.0)

Applies to the Community `hr` app. Our 13.0 release notes describe Enterprise HR apps (Payroll, Appraisal, Approvals, Fleet) but say nothing specific about Community `hr`; everything below comes from the actual 12.0 → 13.0 code changes.

## What's new for users

**Public employee profile.** Employee data is now split in two: the private record (`hr.employee`, HR officers only) and a read-only public profile (`hr.employee.public`) readable by every internal user. A regular employee can no longer read the private record.

**My Profile.** A user linked to an employee gets a "My Profile" screen replacing the standard Preferences, where they view and edit their own HR data (work information, address, emergency contact, bank account…). The new *Employee Edition* setting decides whether employees may edit it themselves or must ask an HR officer.

**Onboarding / offboarding plans.** A new "Activity Plans" configuration groups activities with a responsible (coach, manager, employee or another user) and a deadline; sample Onboarding and Offboarding plans are provided. Creating an employee posts a message linking to a plan, a "Launch Plan" button appears on the employee form, and archiving an employee opens a "Register Departure" wizard (reason + optional plan).

**Departure tracking.** New Departure Reason (Fired/Resigned/Retired) and Additional Information fields, cleared when the employee is reactivated.

**Presence state.** One consolidated status (Present / Absent / To Define), based on login by default, shown as a chip on the kanban and a stat button on the form; Attendance, Time Off and advanced presence options refine it. "Last Activity" is shown for logged-out users.

**Photos** now use the platform image mixin, with the renames listed below.

## Technical data model changes

- New models: `hr.employee.base` (abstract, shared fields), `hr.employee.public` (SQL view, `_auto=False`, read-only), `hr.plan`, `hr.plan.activity.type`.
- `hr.employee` inherits `hr.employee.base` and `image.mixin`. Moved to the base model: name, active, department_id, job_id, job_title, company_id, address_id, work_phone, mobile_phone, work_email, work_location, user_id, resource_id, resource_calendar_id.
- Private fields (notes, color, barcode, pin, departure_reason/description, message_main_attachment_id…) now carry `groups="hr.group_hr_user"`.
- Images: `image_medium` → `image_128`, `image_small` → `image_64`, `image` → `image_1920`, plus new image_1024 / image_512 / image_256; `image_1920` is the writable one.
- New constraint: one employee per user per company.
- `res.users`: many related fields to its employee (job_title, department_id, work_location, address_home_id, bank account, birthday, gender…), exposed through SELF_READABLE/WRITEABLE_FIELDS, plus stored `employee_id`, `hr_presence_state` and `last_activity`.
- `res.company` gains presence settings; presence config parameters move to the `hr_presence.*` namespace.
- Many HR fields are now tracking-enabled.
- `hr.employee` overrides `name_get`, `read`, `_search`, `get_formview_id` and `get_formview_action` to fall back to the public profile for non-HR users; `message_post` / `_message_log` post as the real user instead of OdooBot.

## How your habits should change

- Employees no longer see the full employee form: they use the public profile or My Profile. Grant HR officer rights to anyone needing private or payroll data.
- Private notes and chatter are no longer visible to all employees — a real gain for confidentiality.
- Archiving an employee now opens the departure wizard; reactivating clears the departure fields.
- Users can edit their own data only if *Employee Edition* (or HR rights) allows it, otherwise they get an access error.
- Update exports, integrations and custom reports using `image`, `image_medium` or `image_small`: those names changed.
- One user per employee per company is now enforced by the database.

## What you gain by migrating

- A clean private/public split of HR data, without custom security workarounds.
- Employee self-service (My Profile): less HR administration, fresher data.
- Structured, checklist-driven onboarding and offboarding.
- Better traceability through tracking on most employee fields.
- Modern image handling and a consistent presence indicator across apps.
- A supported, future-proof base: 12.0 is out of support.
