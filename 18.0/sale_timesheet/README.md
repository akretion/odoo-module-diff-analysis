# sale_timesheet migration guide (17.0 -> 18.0)

## What's new for users

The official 18.0 release notes barely touch `sale_timesheet`; the entry that matters is **Analytic plans on projects** (Project app):

- A project no longer carries one single analytic account. It now offers an analytic notebook where **multiple analytic accounts can be set, one per analytic plan**, so costs and revenues can be tracked on several axes at the same time.
- **Timesheets inherit their analytic distribution** from the project, or with priority from the analytic distribution of their Sales Order Item.
- Analytic entries can also be generated from other documents (e.g. stock pickings) so project profitability reflects more than timesheets alone.
- Related records such as timesheets and invoices can be opened from the **top bar of the task Kanban view**.

Other 18.0 Timesheets release notes (spot missing hours in the grid, weekend toggle) belong to the Timesheets app rather than to this addon.

## Technical data model changes

Code organisation: the addon was refactored into `models/`, one file per model (PR 160547). Class definitions moved between files, with no data change.

- **project.project**: `analytic_account_id` is gone; panel/profitability code now uses `account_id` (project account) plus per-plan analytic distribution. `_compute_billable_percentage` removed, and `action_make_billable` (the project → sales order wizard) removed. New helpers `_get_domain_from_section_id`, `_get_foldable_section`.
- **project.task**: `so_analytic_account_id` field and `_compute_analytic_account_active` removed; `_inverse_partner_id` added. Sale-order/timesheet fields (`sale_order_id`, `timesheet_product_id`, `remaining_hours_so`, ...) are unchanged but now live in `project_task.py`.
- **project.task.recurrence**: `sale_timesheet` no longer extends this model (`_get_recurring_fields_to_copy` adding `so_analytic_account_id` was dropped).
- **account.analytic.line**: `_compute_allowed_so_line_ids` and `_default_sale_line_domain` replaced by `_domain_so_line`. New `_is_updatable_timesheet` (blocks SO line changes on billed timesheets), `_timesheet_convert_sol_uom`, and `_timesheet_preprocess_get_accounts` (takes accounts from the SO line distribution; raises a validation error when a mandatory analytic plan is missing).
- **sale.order / sale.order.line**: `_reset_has_displayed_warning_upsell_order_lines` added on the order; order lines moved to `sale_order_line.py` with unchanged fields (`remaining_hours`, `timesheet_ids`, `has_displayed_warning_upsell`).
- **product.template**: `_compute_product_tooltip` replaced by `_prepare_invoicing_tooltip`.
- **project.sale.line.employee.map**: new `_domain_sale_line_id`.
- "Project Updates" is renamed **Dashboard** in the UI.

## How your habits should change

- Stop relying on the project's single "Analytic Account": configure the analytic distribution on the project (one account per plan) and, for billable work, on the Sales Order Item. Since timesheets prefer the SO item distribution, that line must be filled in correctly.
- If an analytic plan is mandatory for timesheets, the Sales Order Item distribution must contain it, otherwise logging a timesheet raises an error.
- Use the project form directly to make a project billable — the old "create sales order from project" action is gone.
- Once a timesheet is billed, its Sales Order Item can no longer be changed.
- Recurring tasks no longer copy the sale order analytic account.

## What you gain by migrating

- **Multi-axis profitability**: analyse project costs and revenues per team, department, service or customer instead of one single analytic account.
- **Less manual encoding**: timesheet analytic distribution follows the Sales Order Item automatically, with validity controls on mandatory plans.
- **Safer billing**: refined SO line selection, locked SO line on billed timesheets, upsell warnings that can be displayed again.
- **Better UX**: invoicing tooltips on products and quick access to related timesheets/invoices from the task top bar.
- **Easier maintenance**: models split per file simplify customisations and OCA-style modules built on top of `sale_timesheet`.
