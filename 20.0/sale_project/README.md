# sale_project migration guide (19.0 -> 20.0)

The official 20.0 release notes do not cover `sale_project` specifics. This guide is therefore built from the 19.0 -> 20.0 code diff (~39 KB, 2 patch sets: 1 modified method signature, 18 added, 27 removed).

## What's new for users

- **Projects on draft sale orders.** The restriction that required a confirmed sale order before linking a project is gone: the overrides on `project.project` and `project.task` that enforced the link (`_ensure_sale_order_linked`, `create`, `write`) were removed. You can now prepare a quotation and its project at the same time.
- **Switching a project to "non-billable" now cleans up its links.** Products and sale orders that pointed to the project are detached automatically instead of being left in an inconsistent state.
- **Sale order warnings are shown on tasks.** A computed warning text (`_compute_sale_warning_text`) exists on both the project and its tasks, so users see configuration problems without opening the sale order.
- **Actual margin per project.** A new `actual_margin` amount and an "Actual Margins" action open the analytic report filtered on the project's analytic account. Companion real cost / real margin indicators were also added.
- **The "Project" field on a sale order can be driven as required** depending on the order content (new computed rule).
- **Analytic lines are now classified.** Each analytic line carries a stored "Billable Type": Revenues Fixed Price, Revenues Milestones, Revenues Manual, Service Revenues, Other Revenues, Other Costs. This feeds the updated analytic/timesheet reporting categories.

## Technical data model changes

Models dropped from this addon:
- `account.move` inheritance removed entirely (`_get_action_per_item` gone).
- `project.update` inheritance removed (`_get_template_values` gone): the project Updates/dashboard report no longer embeds sale order line details.

`project.project`: added `actual_margin` (Monetary, computed), `_compute_actual_margin`, `action_actual_margin`, `_compute_real_cost`, `action_real_margin`, `check_allow_billable_projects`, `_unlink_billable_products`, `_fetch_linked_products`, `_fetch_linked_sale_orders`, `_compute_sale_warning_text`, `_onchange_partner_id`. Removed about twenty profit-related methods (`action_profitability_items`, `get_panel_data`, `get_sale_items_data`, `_get_items_from_invoices*`, `_get_profitability_*`, `_get_stat_buttons`, `_show_profitability*`).

`account.analytic.line`: new stored computed field `billable_type`; new methods `_compute_project_billable_type`, `_compute_category_report`, `_get_invoice_type`, `_set_billable_cost`, `action_open_account_analytic_line_origine`.

`project.task`: `create` and `write` overrides plus `_ensure_sale_order_linked` removed; `_compute_sale_warning_text` added.

`sale.order`: `_compute_project_required` and `_get_or_create_analytic_account` added.
`sale.order.line`: `_compute_qty_delivered_method` and `_get_action_per_item` removed; `_prepare_qty_delivered` and `action_view_sale_order` added.
`sale.order.template.line`: `_prepare_order_line_values` now accepts `*args, **kwargs`.

Where the profitability calculation went is outside this addon: it is carried by a separate margins module. Confirm with your integrator which modules and which edition you actually run before relying on the previous profitability screens.

## How your habits should change

- You no longer have to confirm a sale order before attaching a project to it.
- Review projects you switch to non-billable: linked products and sale orders are now detached, which changes what appears in billing views.
- The profitability panel and its drill-down actions are no longer served by `sale_project`; the equivalent overview comes from the separate margins module.
- The project Updates report no longer lists sale order lines; use the project form statistics and the analytic report instead.
- Expect analytic report figures to be split by the new billable type categories.

## What you gain by migrating

- Fewer blocked workflows: quotations and projects can be built together.
- Less inconsistent data when billing settings change on a project.
- Faster diagnosis of billing mistakes thanks to the sale order warning on tasks.
- Project-level margin, real cost and real margin figures available from the project itself.
- Cleaner analytic reporting with a structured revenue and cost classification.
