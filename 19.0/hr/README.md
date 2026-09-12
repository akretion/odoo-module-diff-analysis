# hr migration guide (18.0 -> 19.0)

## What's new for users

The official 19.0 release notes do not describe the core `hr` app (the Payroll, Planning, Expenses and Documents sections cover other apps, mostly Enterprise-only packages). Everything below comes from the 19.0 code of `hr`.

- **Multiple bank accounts per employee.** The single "Bank Account" field becomes a list. A new *Bank Account Allocation* wizard lets HR split the salary across accounts, as a fixed amount or as a percentage of the remainder, and reorder accounts to set priority: the first one is the primary account, used for expense receipts and payments. A dedicated widget shows each account's allocation.
- **Contracts become employee versions.** Wage, dates, working schedule and salary structure now live on the employee as dated versions. New indicators show whether a version is current, future or past, and a version list view is reachable from the employee form.
- **Clearer work location data.** Work location name and type (Home / Office / Other) are now computed on the employee, so the presence icon tooltip and user profiles show meaningful values instead of raw location names.
- **Smaller improvements:** public birth-date string, bulk user creation from the Employees list, automatic phone number formatting, clearer address/state selection, avatar card data in multi-company.
- **User profiles.** Many employee fields (birthdate, marital status, ID, permit and visa numbers, studies, presence, home-work distance, language…) are no longer related on `res.users`; that data is now managed from the employee record.

## Technical data model changes

- `hr.employee.bank_account_id` (Many2one) → `bank_account_ids` (Many2many via `employee_bank_account_rel`). New fields: `primary_bank_account_id`, `is_trusted_bank_account`, `has_multiple_bank_accounts`, and stored JSON `salary_distribution` (percentage allocations must total 100%).
- `res.partner.bank.employee_id` becomes Many2many (with `_search_employee_id`), plus `employee_salary_amount`, `employee_salary_amount_is_percentage`, `currency_symbol`, `employee_has_multiple_bank_accounts`.
- `res.users`: `employee_bank_account_id` / `bank_account_id` → `employee_bank_account_ids` / `bank_account_ids`; many related employee fields removed (department, address, parent, coach, birthday, marital, sex, IDs, visa, permit, certificate, studies, presence/activity, employee type…). `_compute_can_edit` removed; `_compute_is_hr_user`, `action_related_contact`, `get_formview_action` added.
- `hr.version` carries the contract data now: wage, dates, structure, flexible schedule, with new computes (`_compute_is_current`, `_compute_is_future`, `_compute_is_past`, `_compute_contract_wage`, `_compute_job_title`…), `create`, `write`, `action_open_version`, `check_contract_finished`, `_unlink_except_last_version`. `work_location_name` / `work_location_type` are removed from `hr.version` and computed on `hr.employee` (related on `hr.employee.public`).
- `hr.employee`: new `_compute_current_version_id`, presence/newly-hired computes, `create_version`, `create_contract`, `check_no_existing_contract`, `action_archive` / `action_unarchive` (replacing the deprecated `toggle_active`), `_cron_update_current_version_id`. Removed: `_check_ssnid`, `_cron_check_work_permit_validity`, `_get_marital_status_selection`.
- `hr.department`: new `_search_complete_name` search method.
- ORM signature updates: `_search(..., bypass_access=False)`, `fetch()` / `search_fetch()` with optional `field_names`, `_field_to_sql(alias, field_expr, query=None)`.

## How your habits should change

- Stop looking for a separate Contract object: open the employee and use the contract/version actions. Existing contracts are migrated into versions, and the "new contract" button warns when a version already covers the date.
- Manage payroll bank details through the allocation wizard instead of one field, and keep percentage allocations totalling 100% — Odoo refuses invalid distributions.
- Edit personal data (birthday, family situation, IDs, permits) on the employee, not on the user's profile.
- Archive employees through the archive/unarchive actions, which also work on several employees at once.

## What you gain by migrating

- One source of truth per employee: identity, contract history and payroll data in a single versioned record.
- Genuine salary splitting across several bank accounts, with a primary account for payments and a controlled allocation total.
- Fewer inconsistencies between employee, user and contact records, cleaner profile permissions, and an up-to-date ORM surface for your customizations built on `hr`.
