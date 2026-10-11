# sale_timesheet migration guide (17.0 -> 18.0)

## What's new for users

- **Analytic plans on projects.** Projects no longer carry a single analytic account. You now assign an analytic distribution (one account per plan) on the project, and timesheets inherit it. When a timesheet is linked to a sale order item, that item's analytic distribution takes priority. This lets you track a project's costs and revenues on several dimensions at once (department, team, service, customer...).
- **Top bar navigation.** From the task views you can jump to related records (sale orders, timesheets, invoices) using embedded actions in the control panel.
- **Timesheets grid view** (Timesheets app, not specific to sale_timesheet): spot under-assigned hours quickly, and toggle weekends on/off.

The rest of the 18.0 release notes (localizations, accounting, POS, spreadsheet...) do not affect this addon.

## Technical data model changes

- Code is split into a `models/` folder with one file per model (`account_move_line.py`, `hr_timesheet.py`, `product_product.py`, `product_template.py`, `project_project.py`, `project_task.py`, `project_update.py`, `sale_order.py`, `sale_order_line.py`, ...). No functional impact, but custom patches or overrides pointing at the old file names must be updated.
- `project.task`: the `so_analytic_account_id` field (related to `sale_order_id.analytic_account_id`) is removed, together with the `_compute_analytic_account_active` override that extended analytic account activity from the sale order.
- `project.task.recurrence`: `sale_timesheet` no longer inherits this model; the `_get_recurring_fields_to_copy` override adding `so_analytic_account_id` is gone.
- `project.project`: the single `analytic_account_id` is replaced by `account_id`. Profitability/panel data keys change from `analytic_account_id` to `account_id` in `project.project` and `project.update` (Dashboard).
- `sale.order`: `analytic_account_id` is removed; a project is used instead, and the project's analytic accounts feed the sale order line distribution.
- `account.analytic.line` (timesheets): new `_timesheet_preprocess_get_accounts(vals)`. It builds the timesheet's analytic distribution from the linked sale order item, and raises a `ValidationError` when a plan that is mandatory for timesheets (business domain `timesheet`) is missing from that distribution.
- General cleanup: `super()` calls modernised, Python 3 style; no signature change on the remaining public methods.

## How your habits should change

- Stop looking for an "Analytic Account" field on projects, tasks and sale orders: it is gone. Configure analytic distribution on the project (and on your quotation lines), never on tasks.
- Tasks hold no analytic account anymore, and recurring tasks no longer copy one.
- When a timesheet is linked to a sale order item, check that item's analytic distribution: it must contain every plan that is mandatory for timesheets, otherwise Odoo blocks the save and names the missing plan and the sale order line.
- For billable projects, rely on the project / sale order line distribution rather than entering analytic accounts manually on each timesheet.

## What you gain by migrating

- Multi-plan (multi-dimensional) profitability for projects and timesheets, instead of one analytic axis.
- Less manual entry and fewer errors: the analytic distribution of a timesheet follows the sale order item it is billed on.
- A cleaner, maintained code base (one file per model), which eases future upgrades and custom developments.
- You stay on a supported version and benefit from the broader 18.0 project/timesheet improvements (top bar navigation, grid view).
