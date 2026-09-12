# hr migration guide (18.0 -> 19.0)

The `hr` app has been deeply reshaped in 19.0: the contract model disappears in favour of a versioned employee, and bank account handling becomes multi-account. This guide summarises what a functional user and a project owner need to know.

## What's new for users

- **Contract history on the employee form.** The separate contract concept is merged into the employee as "versions". A timeline in the employee form lets you see the succession of versions over time, and you can create past or future versions manually.
- **Protected payroll history.** Editing a version that is already covered by a done or paid payslip no longer silently rewrites history: Odoo creates a new version, flags the affected payslips and schedules an activity for the HR responsible. A version can only be deleted when no payslip refers to it.
- **Multiple bank accounts per employee.** An employee can now hold several bank accounts. A wizard lets HR distribute the salary across them (fixed amount or percentage of the remainder) and reorder them by priority; the first one is the primary account used for expense receipts and payments. Invoices/bills for the employee use that primary account.
- **Leaner user profile form.** The preferences and user form no longer expose a long list of HR fields (birthday, marital status, identification numbers, education, presence, manager, coach, home-work distance, etc.). Those values are now managed from the employee record.

Note: the official 19.0 release notes selected for this addon mostly describe Enterprise payroll, localization, Planning, Documents and industry packages, not the Community `hr` app. Nothing in them can be safely attributed to this module, so all of the above comes from the code changes.

## Technical data model changes

- **New model `hr.version`.** `hr.employee` now uses `_inherits` on it through `version_id`, so version fields stay readable/writable from the employee. New fields: `version_ids` (One2many), `current_version_id`, `current_date_version`, `versions_count`, plus helpers `create_version()`, `_get_version(date)`, `_is_in_contract(date)`.
- **Dates.** `date_start` / `date_end` are replaced by a mandatory `date_version` and the `contract_date_start` / `contract_date_end` pair. On versions, `date_start` / `date_end` are now computed and non-stored.
- **States removed.** Contract states (`open`, `close`, ...) and `kanban_state` are gone; use the computed helpers (`is_current`, `is_past`, `_is_in_contract`).
- **Fields moved to `hr.version`:** department, job, company, address, work contact/user/resource links, working schedule, marital status, spouse and children, identification numbers (SSN/SIN/passport), gender, home-work distance, departure reason/date/description, work location.
- **Bank accounts.** `bank_account_id` (Many2one) becomes `bank_account_ids` (Many2many) on `hr.employee`, with `primary_bank_account_id`, `is_trusted_bank_account`, `has_multiple_bank_accounts` and a stored JSON `salary_distribution`. `res.partner.bank.employee_id` becomes a Many2many with a custom search; `res.users.employee_bank_account_ids` follows.
- **`res.users` cleanup.** Dozens of related HR fields and their entries in `HR_READABLE_FIELDS` / `HR_WRITABLE_FIELDS` were removed, and the HR-specific simple user form view was merged away.
- **Work location.** `work_location_name` / `work_location_type` move from `hr.version` to `hr.employee` as computed fields; `hr.employee.public` now relates to them.
- **Misc.** `hr_icon_display` selection is now extensible; the bank allocation wizard model was added. Reporting/SQL that read version fields must join `hr.version`.

## How your habits should change

- Stop managing "a contract per employee": manage versions on the employee, and use the timeline to navigate history.
- Expect a new version (and payslip warnings) instead of an in-place edit when touching a version already paid.
- Salary rules and localizations must read `version` from `localdict` instead of `contract`, and custom domains based on `date_start` / `state` must be rewritten.
- For payouts, select accounts with the multi-account field and open the allocation wizard to set amounts/percentages; keep the order meaningful, as it defines the primary account.
- Manage personal/HR data on the employee form, not on the user form.
- Review custom reports, SQL queries and integrations touching `hr.contract`, `date_start`, `state` or `bank_account_id`.

## What you gain by migrating

- A single source of truth for employee data with a real, auditable contract history.
- Safer payroll: modifications on already-paid periods are detected and turned into new versions with traceability.
