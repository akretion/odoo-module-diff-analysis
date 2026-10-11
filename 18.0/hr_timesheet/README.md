# hr_timesheet migration guide (17.0 -> 18.0)

## What's new for users

The release notes' *Timesheets* section only discusses grid-view enhancements (spotting missing hours, weekend toggle), an Enterprise-edition feature that is not part of this Community addon. The one relevant 18.0 item for hr_timesheet is listed under *Project*: **Analytic plans on projects**.

A project is no longer limited to a single analytic account: it now carries one analytic account **per analytic plan**, entered in a new analytic notebook on the project form. Timesheets no longer let users choose an accounting destination; they inherit the analytic distribution of their project, or of the linked sales order line (which takes priority). One timesheet cost can therefore feed several axes at once — project, team, department, service — for finer profitability analysis, while project profitability still follows the account of the *Projects* plan.

Related 18.0 improvements (analytic entries from stock pickings, re-invoicing of delivered products, project on PO/MO) belong to other addons, not hr_timesheet.

## Technical data model changes

Source: odoo/odoo#169868 (analytic plans on projects). No model is added or removed in hr_timesheet.

Fields:
- `project.project.analytic_account_id` is renamed to **`account_id`**.
- `project.project.analytic_account_active` is now `related='account_id.active'`.
- `project.task.analytic_account_active` is now `related='project_id.analytic_account_active'`; `_compute_analytic_account_active()` is removed. `analytic_account_id` was dropped from `PROJECT_TASK_READABLE_FIELDS`: tasks no longer expose an analytic account to timesheets.
- `_create_analytic_account()` and `_init_data_analytic_account()` now use `account_id`.

New applicability:
- New file `models/analytic_applicability.py` extends `account.analytic.applicability` with the `business_domain` value `('timesheet', 'Timesheet')` (ondelete cascade): a plan can be flagged as required for timesheets.

`account.analytic.line`:
- `_timesheet_preprocess(vals_list)` is **removed**.
- `_timesheet_preprocess_get_accounts(vals)` is **added**: it returns one account id per analytic plan column of the project, and raises if a mandatory timesheet plan has no account on that project.
- `create()` and `write()` now resolve task/project themselves, block timesheets on private tasks in both, and force `company_id` from the project/task; `create()` also defaults `product_uom_id` to the company's project time mode.
- `_timesheet_postprocess()` adds two checks: the analytic account must be active (the error names the required plan), and project, task and analytic accounts must belong to the same company.
- Error wording changed: "You cannot use timesheets without an analytic account" became "To use the timesheets feature, you need an analytic account for your project... set one up in the plan '<Plan>' or turn off the timesheets feature."

## How your habits should change

- Do not pick an analytic account when logging time: it now comes from the project (or the sales order line).
- Task-level analytic accounting is gone; move that setup to the project's analytic notebook. A task only mirrors its project's active account.
- Fill in the analytic notebook per project: the *Projects* plan account must exist and be **active**, otherwise timesheet creation is blocked.
- If a plan is flagged as required for the *Timesheet* domain, every timesheet-enabled project needs an account for that plan.
- Keep project, task and analytic accounts in the same company.
- Update custom filters, exports, saved views and automated actions that reference `analytic_account_id` on projects or tasks.
- Retrain users on the new error messages.

## What you gain by migrating

- Multi-axis time costing: one timesheet feeds several analytic plans at once.
- Less manual entry and fewer mistakes: no per-timesheet account selection.
- Clear guard rails: explicit errors when a plan or account is missing or inactive.
- Trustworthy figures: company-consistency and applicability checks.
- A clean base for the wider 18.0 project analytic features (stock, MRP, purchase, re-invoicing).
