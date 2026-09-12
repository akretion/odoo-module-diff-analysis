# hr migration guide (13.0 -> 14.0)

This guide covers what changes for the core **Employees (hr)** app when moving from Odoo 13.0 to 14.0. The 14.0 release notes for "Employees" mostly describe other HR addons (Time Off, Contracts, Payroll, Appraisals, Planning, Documents — several of which are Enterprise-only), so the change that really impacts the *hr* addon is a data-model cleanup: form "onchange" logic has been turned into stored, editable computed fields.

## What's new for users

Only a few release-note items concern the core hr app, in Community:

- **Mass editing of HR data** — Departments, job positions, and managers can now be updated for many employees at once directly from the list view.
- **Discuss integration** — Click a colleague's avatar to start a chat right away.

Other items in the Employees release notes (salary configurator, Planning availability, Documents, payroll credit days) belong to addons outside *hr*, and some are Enterprise features. We do not list them as part of this migration.

## Technical data model changes

Source: PR 45414, *"[REF] hr*: convert onchange methods to stored-editable computed fields"* (Task 2169099).

**Removed onchange methods** on `hr.employee`: `_onchange_job_id`, `_onchange_address`, `_onchange_company`, `_onchange_department`.

**Changed fields** (model `hr.employee` / `hr.employee.base`): from plain editable fields to *stored, computed, still editable* fields (`compute=..., store=True, readonly=False`):

| Field | Computed from |
|---|---|
| Job Title (`job_title`) | Job Position (`job_id`) |
| Work Address (`address_id`) | Company (`company_id`) |
| Work Phone (`work_phone`) | Work Address (`address_id`) |
| Work Mobile (`mobile_phone`) | Work Address (`address_id`) |
| Manager (`parent_id`) | Department (`department_id`) and its manager |

**Changed field** on `hr.plan.activity.type`: `summary` is now computed from the selected Activity Type and only fills in when left empty.

Technical consequence: these values are now written to the database when their trigger field changes — not only in the form view. Imports, API calls, and mass edits therefore produce the same defaults as the user interface. Two onchanges were kept because of complex behaviour: the timezone (`tz`) and the name (`name`) linked to the resource.

## How your habits should change

Behaviour in the form is essentially the same, but it is now consistent everywhere:

- **Setting a Job Position overwrites Job Title**; setting a **Department overwrites Manager**; changing the **Company overwrites the Work Address**; changing the **Work Address overwrites Work Phone and Work Mobile**. This already happened in the form; it now also happens on import, API, and mass-edit writes.
- **Manual entries are preserved** as long as the trigger field is not touched again — these are ordinary editable stored fields.
- **When importing or scripting employee data**, write the "trigger" fields first and the derived fields last, otherwise a later change to Job Position, Department, Company, or Work Address can silently reset your Job Title, Manager, or phone numbers.
- **Mass edit carefully:** editing a job position or department for a whole list of employees will also recompute the related job title / manager.
- **Better reporting:** since the values are stored, they can be filtered, grouped, and exported reliably (previously, form-only values could be missing on records created outside the UI).

## What you gain by migrating

- **Consistent employee data everywhere** — job titles, managers, work addresses, and phone numbers are filled the same way whether the record is created from the UI, an import, or an integration.
- **Faster HR maintenance** — mass editing of departments, job positions, and managers from the list view removes hours of repetitive form-by-form work.
- **Reliable reporting and exports** on those fields, which were previously filled only interactively.
- **A cleaner, more maintainable data model** built on standard Odoo computed-stored fields — fewer surprises for customizations and future upgrades.
- **Continued support and security fixes**: 13.0 is an old branch; moving to 14.0 (and beyond) keeps you on a maintained, upgradeable platform.

*Tip:* because derived fields now depend on their trigger fields, plan a quick data review after migration — check that job titles, managers, and phone numbers match your expectations, especially for records maintained through imports.
