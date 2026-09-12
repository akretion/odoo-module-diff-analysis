# hr migration guide (16.0 -> 17.0)

Odoo 17.0 reworks the core Employees addon (`hr`) on two axes: private employee information is stored on the employee itself, and HR plans become generic activity plans.

## What's new for users
From the 17.0 release notes (Employees section), the items that concern the Community `hr` addon:
- Employee widget: every field referring to an employee now displays the employee's avatar.
- New graph and pivot views on employees.
- New organizational chart view on employees and departments.
- New "Newly hired" filter in the Kanban view.
- The presence indicator coming from Attendances is visible even without any Attendance rights.

Other items listed under Employees in the official notes (signature requests, HR files link, certification report, CV generation) belong to Enterprise apps and are not part of this Community addon.

## Technical data model changes
Removed on `hr.employee`: `address_home_id` (M2O res.partner), `is_address_home_a_company`, `phone` (was related to the private partner), `related_contact_ids`, `related_contacts_count`. The `action_related_contacts()` method and the private-address subscription/archiving logic are gone too.

Added on `hr.employee`: `private_street`, `private_street2`, `private_city`, `private_state_id`, `private_zip`, `private_country_id`, `private_phone`, `private_email`. `lang` is no longer related to the partner; it is a selection on the employee (new `_lang_get` helper).

Behaviour changes:
- `bank_account_id` domain is now based on `work_contact_id` (was `address_home_id`); `write()` re-syncs the bank account partner and chatter subscriptions on `work_contact_id`.
- `res.partner.employee_ids` ("Employees") now points to `hr.employee.work_contact_id`; the `_compute_display_name` override for private addresses was removed.
- `res.users`: `address_home_id`, `employee_phone` and `is_address_home_a_company` removed; `work_contact_id`, `private_phone`, `private_email` added as related fields to the employee.
- `hr.employee.public`: `related_contact_ids` and its compute removed.

Plans:
- Models `hr.plan`, `hr.plan.activity.type` and `hr.plan.employee.activity` are removed and replaced by the generic `mail.activity.plan` / `mail.activity.plan.template` (extended in `hr`).
- `hr.department.plan_ids` now targets `mail.activity.plan`.
- Plan templates gain the employee-specific responsible types `coach`, `manager`, `employee`, allowed only when the plan's `res_model` is `hr.employee`.
- Signature change: `get_responsible_id(employee)` is replaced by `_determine_responsible(on_demand_responsible, employee)`, which returns an `error` key instead of `warning`.

## How your habits should change
- Enter the employee's private address, phone and email directly on the employee form (private information section) instead of creating and linking a separate contact. Check during the migration that the data previously stored on that contact has been transferred.
- The "Related Contacts" button and counter disappear; use the work contact and user links of the employee instead.
- Configure and launch onboarding/offboarding through Activity Plans. A plan with a department, or with coach/manager/employee responsibilities, must be dedicated to employees; a free "other responsible" is replaced by the generic responsible of the activity template.
- Launch a plan from the chatter's activities button; the dedicated "Launch plan" button was removed.
- For HR contracts and Fleet, when a plan is launched for several records, the default due date is now always the earliest first-contract date (previously it could be the first one).

## What you gain by migrating
- A single, clearer employee form: no more secondary partner record to create and maintain for each employee; fewer duplicates in your contacts.
- Private data is kept on the employee record under HR groups, with the ad-hoc partner `display_name` workaround removed — simpler and more secure.
- One plan engine shared with the rest of Odoo (mail activities) instead of an HR-specific one: less duplicated configuration, no manual "launch plan" step.
- Standard 17.0 usability gains in the app: avatar-based employee widget, graph/pivot views, org chart, newly hired filter, presence icon.
- Staying on 16.0 means running an unsupported version: migrating keeps you aligned with current localizations and OCA modules, and prepares future upgrades.
