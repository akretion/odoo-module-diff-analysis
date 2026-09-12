# hr migration guide (14.0 -> 15.0)

## What's new for users

- **Configurable departure reasons.** You can now define your own departure reasons in the Employee configuration instead of using a fixed list; they become selectable when an employee leaves.
- **Cleaner archiving of managers and coaches.** When a manager or coach is archived, the employees linked to that person are no longer attached to them, so nobody keeps reporting to an inactive colleague.
- **Distinguishable avatars.** Employees without a photo no longer share the same generic placeholder: an avatar is generated from their initials with a random colour, making people easier to tell apart in lists, kanban and chatter.

The release notes also mention appraisals created from the action menu, employees editing their languages, and work permits. Those belong to Enterprise apps (Appraisal, Payroll/Documents) and are not part of this Community addon.

## Technical data model changes

**New model:** `hr.work.location` (name, company_id, address_id "Work Address", active), ordered by name.

`work_location` (Char) is removed and replaced by `work_location_id` (Many2one to `hr.work.location`) on `hr.employee` / `hr.employee.base`, `hr.employee.public` and `res.users`. On the employee it is computed from `address_id` and cleared whenever the address changes; a domain limits the choice to locations matching the employee's address and company.

**`hr.employee`:** inherits `avatar.mixin` instead of `image.mixin`; `image_1920` is no longer declared locally and the placeholder-filename hook is gone. New `_compute_avatar_*` methods per size fall back to the linked user's avatar, or to a generated initials placeholder.

**`hr.employee.public`:** new computed `employee_id` Many2one (with its own search method); image fields become *related* to that employee instead of computed; new related `avatar_*` fields.

**`hr.plan`:** removed `plan_type`, `trigger_onboarding`, `trigger_offboarding`, `trigger_other`, `trigger`, `company_id` and the related compute/inverse methods.

**`hr.plan.activity.type`:** removed `deadline_type`, `deadline_days`, `company_id` and the SQL constraint on `deadline_days`.

**`hr.employee` methods:** removed `_launch_plans_from_trigger()` and `_launch_plan()`; onboarding/offboarding is no longer triggered from employee creation or archiving.

## How your habits should change

- Work location is now a dropdown, not free text. Create your sites first in the Work Locations list (each one needs a work address); the field then only proposes locations matching the employee's address/company, and is emptied when that address changes. Existing text values must be mapped during the upgrade.
- Onboarding and offboarding plans are no longer launched automatically on employee creation or archiving. Launch them yourself from the employee's plan action; a chatter message still suggests setting one up.
- The per-step deadline options (default value, at activation, X days after the trigger) disappear from plan activities, which now use standard activity deadlines.
- Archiving a manager or coach no longer leaves subordinates pointing at an archived person.
- Employee pictures: staff without an uploaded image now show generated initials — upload a photo only when you really want one.

## What you gain by migrating

- Reliable, filterable work-location data: group and report by site and address instead of cleaning up inconsistent free text.
- Fewer surprises: no activities silently created by plan triggers, so you stay in control of onboarding and offboarding.
- A more readable HR app, with distinguishable avatars and consistent images across employee, public-employee and user views.
- Departure reasons that match your own HR policy rather than a fixed list.
- A code base aligned with the supported 15.0 branch: easier maintenance, security fixes and future upgrades.
