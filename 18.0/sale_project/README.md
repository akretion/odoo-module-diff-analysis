# sale_project migration guide (17.0 -> 18.0)

## What's new for users

- **Analytic plans on projects**: project costs and revenues can be split across several analytic plans at once (project, department, customer...) instead of a single analytic account, for much finer profitability analysis and budgeting.
- **Costs and revenues from stock and manufacturing**: validating stock pickings linked to a project (plus landed costs, purchase orders and manufacturing orders) can post analytic entries, so materials consumed on a site show up in the project's profitability.
- **Automatic re-invoicing**: products delivered for a project can be re-invoiced on the sales order set on that project, instead of adding lines manually to the quotation.
- **Products creating tasks**: service products can be configured to create their task in the project chosen on the quotation.
- **Embedded actions**: related records (sales orders, purchase orders, pickings, analytic items) are reachable from the project's top bar.

These changes span Sales, Purchase, Stock and MRP, and require configuration (e.g. analytic costs on operation types).

## Technical data model changes

- `project.project`: `analytic_account_id` is gone, replaced by the analytic notebook / `account_id` (one analytic account per plan). New `reinvoiced_sale_order_id` ("Sales Order") used for re-invoicing, with onchanges keeping it in sync with `sale_line_id`. `vendor_bill_count` now follows `account_id`.
- `sale.order`: new `project_id` (domain: billable projects) and related `project_account_id`. `analytic_account_id` and the `_generate_analytic_account` override are removed; `_prepare_analytic_account_data` is reimplemented.
- `sale.order.line`: `_compute_analytic_distribution` now derives the distribution from the product's project/template or the order's project. `_timesheet_create_project_prepare_values` no longer sets an analytic account; new `_timesheet_create_project_account_vals`; project creation sets `reinvoiced_sale_order_id`. Confirming a "task in global project" line without a project on the SO or product raises a `UserError`. The SO's `project_id` is auto-set to the first generated project when it was empty. Invoice line preparation reads analytic accounts from the project, not from tasks.
- `project.task`: `analytic_account_id` is removed; timesheets inherit analytic accounts from their project or from their sale order line (sale line has priority).
- `account.move.line`: distribution is built from the project's analytic accounts; new `_get_so_mapping_from_project` / `_sale_determine_order` map vendor bill lines to the sale order to re-invoice, matching on the project's analytic accounts.
- Model files were split one per model (e.g. `project_project.py`, `sale_order_line.py`, `project_task.py`). No functional change, but customizations inheriting the old file layout may need updating.
- Terminology/UI: "Project Updates" is renamed "Dashboard"; stat buttons and top-bar actions were reworked accordingly.

## How your habits should change

- Stop setting an analytic account on projects and sales orders: use the project's analytic notebook and, on quotations, the new **Project** field.
- Tasks are no longer analytic-aware: allocate costs through the project, or through the sale order line used for invoicing.
- For re-invoicing, fill the project's **Sales Order** field; vendor bills are now re-invoiced based on the SO's project rather than the SO's analytic account.
- Products with "task in global project" tracking must have a project on the product or on the quotation, otherwise order confirmation is blocked.
- Check your operation types if you want pickings to generate analytic costs and re-invoicing.

## What you gain by migrating

- Multi-dimensional analytic accounting: profitability by project, team, customer or service at the same time, and better budgeting.
- Material and manufacturing costs become visible in project profitability, with pickings, POs and MOs reachable directly from the project.
- Less manual work and fewer errors when re-invoicing products delivered to a project.
- A cleaner, more granular reporting foundation built on the 18.0 analytic plan framework.
