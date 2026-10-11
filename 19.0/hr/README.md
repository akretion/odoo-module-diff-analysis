# hr migration guide (18.0 -> 19.0)

## What's new for users

**Employee versions replace contracts.** The HR app now keeps a time-based history of each employee's contract directly on the employee form. A timeline shows past, current and future versions, so you can see at a glance what applied when, without opening a separate contract record.

**Multiple bank accounts.** Employees can now register several bank accounts. A dedicated allocation wizard lets HR split the salary across accounts, as a fixed amount or a percentage of the remainder. Accounts can be reordered to set the priority; the first one becomes the primary account used for expense receipts and payments. A new widget shows the amount allocated to each account.

**Simpler user form.** The user / preferences form has been cleaned up: many HR fields that duplicated the employee form were removed, and how users edit their own information was reworked. Work location name and type are now surfaced on the employee (and public employee) record.

The 19.0 release notes contain no item specific to the Community `hr` addon (their HR-related entries concern localizations or other apps). The changes below therefore come from the 18.0 → 19.0 code differences.

## Technical data model changes

- **New model `hr.version`**, inherited by `hr.employee` via `_inherits`; `hr.employee` no longer inherits `hr.employee.base`.
- Payroll-impacting fields previously on `hr.employee`/`hr.contract` are centralised in `hr.version`: job, department, working calendar, marital status, spouse/children, identification numbers, departure, work location, salary structure.
- `date_start`/`date_end` are replaced by `date_version` (mandatory) and `contract_date_start`/`contract_date_end`; the old dates remain as computed, non-stored fields. Contract states (`open`, `close`, `kanban_state`) were removed.
- `hr.employee.bank_account_id` (Many2one) becomes `bank_account_ids` (Many2many), with new `primary_bank_account_id`, `salary_distribution` (JSON), `has_multiple_bank_accounts` and `is_trusted_bank_account` fields.
- `res.partner.bank.employee_id` becomes a Many2many, with new salary-allocation fields; a new allocation wizard model is added.
- On `res.users`, `bank_account_id`/`employee_bank_account_id` become `..._ids`, and many HR fields (`department_id`, `address_id`, `coach_id`, `birthday`, `marital`, `children`, `identification_id`, `ssnid`, `passport_id`, study/visa fields, `hr_presence_state`, `employee_type`, …) are removed from the model and profile form.
- `work_location_name`/`work_location_type` move from `hr.version` to `hr.employee`; the `hr_icon_display` selection becomes extensible.
- New helpers `create_version()`, `_get_version(date)`, `_is_in_contract(date)`, `_get_contract_dates(date)`. ORM access to version fields stays transparent, but raw SQL must join `hr.version`, and salary rules receive `version` in `localdict` instead of `contract`.

## How your habits should change

- Stop thinking in "contracts": you no longer create an `hr.contract`. You edit the employee's current version, or add one for a past/future period.
- Editing a version already linked to a "Done"/"Paid" payslip now creates a new version, flags the affected payslips and schedules an activity for the HR responsible; such versions can no longer be deleted.
- Creating an employee creates its first version; use `create_version` for additional ones.
- Salary rules and custom domains based on `date_start`, `date_end` or `state` must be adapted (`is_current`, `is_past`, `_is_in_contract`, …).
- Bank accounts: register them all on the employee, then split the salary with the allocation wizard; the top account is the primary one.

## What you gain by migrating

- One reliable, time-stamped source of truth for employee data and pay conditions, with visual history.
- Better payroll safety: changes touching already-paid periods are detected, traced and notified instead of silently rewriting history.
- Salary can be split over several bank accounts, matching real-world payroll practice, while expenses keep using a single primary account.
- Cleaner, less duplicated employee and user forms, and a supported 18.0 → 19.0 upgrade path with Akretion.
