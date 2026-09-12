# analytic migration guide (16.0 -> 17.0)

## What's new for users

- **Cross analytic (analytic cross-reporting).** You can now enter up to one analytic account per plan on each line (invoice line, journal item, timesheet...). Two plans such as "Country" and "Product Type" can therefore be combined in the same pivot table, instead of being limited to one analytic account per line.
- **Plans are shared across companies.** An analytic plan is no longer attached to one company; accounts, applicability rules and analytic items remain company-dependent, so each company still sees its own data.
- **Plans create their own input columns.** Creating or renaming a root plan adds/updates a dedicated field on analytic items, so new plans can be added without development; the "Project" plan stays the reference one.

The 17.0 release notes cover no other analytic-specific novelty; the other Accounting items (reports, bank sync, OCR, Peppol, ...) belong to other addons.

## Technical data model changes

**analytic.mixin** — `analytic_distribution` is now a stored `fields.Json` (in 16.0 it was a computed `Binary` backed by the stored `analytic_distribution_stored_char`, which is removed). A GIN index is created on each model's table, so search on analytic keys is supported. Values are normalized in `create`/`write` via `_sanitize_values` instead of the inverse/onchange; `_get_distributionjson` is removed. Keys may now contain several account ids (comma-separated string). The mandatory-plan check sums percentages across those accounts.

**account.analytic.plan** — removed `company_id` and `_get_default()`; added `root_id` (computed), `sequence` (model now ordered by sequence, id), `_column_name()` and `_get_all_plans()`. `default_applicability` is company-dependent. Creating/renaming a root plan creates or updates a manual field `x_planX_id` on `account.analytic.line`; deleting the plan deletes it. `account.analytic.applicability` gained a `company_id`.

**account.analytic.account** — `plan_id`/`root_plan_id` lose `check_company`; `root_plan_id` becomes `related="plan_id.root_id"`. `line_ids` is linked via the new `auto_account_id` instead of `account_id`. Debit/credit/balance computation reworked per plan.

**account.analytic.line** — added the magic field `auto_account_id` (compute/inverse/search, context key `analytic_plan_id`) representing all plans at once; `account_id` is no longer required and is labelled "Project Account". Removed: `plan_id` (was `related='account_id.plan_id'`) and the `_check_company_id` constraint. One dynamic many2one column per plan; views and filters are extended at runtime (`fields_get`, `_get_view`).

**account.analytic.distribution.model** — `company_id` now defaults to the current company, and the new `_check_company_accounts` constraint rejects a distribution model mixing accounts of a specific company with a shared/other company.

## How your habits should change

- Analytic distribution is still entered the same way, but it is now stored as JSON: keys are account ids and percentages are rounded to the "Percentage Analytic" precision. Existing values are converted by the standard upgrade scripts.
- Analytic accounts are no longer owned by one plan per company: verify your plan and applicability configuration after migration.
- On analytic items, the "Analytic Account" column you see is the magic `auto_account_id`; depending on context it points to the project account or to a plan-specific column.
- Distribution models must stay consistent with company: a model containing a company-specific account must not be shared between companies.

## What you gain by migrating

- True cross-analytic reporting: combine several plans in one analysis instead of one account per line.
- Simpler multi-company analytics: one shared plan tree, with company-specific accounts, rules and data.
- Faster, more reliable analytic search thanks to JSON storage and the GIN index.
- Less customization: new plans automatically add their input columns.
- Data-quality guardrails on distribution models.
