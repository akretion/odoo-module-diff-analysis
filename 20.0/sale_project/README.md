# sale_project migration guide (19.0 -> 20.0)

## What's new for users

The 20.0 change is that **project profitability no longer lives inside the project dashboard**.

- The profitability report / side panel that used to sit in the project dashboard has been **removed**. Per the 20.0 release notes, project budget, actual margin and projected margin are now analysed through **dedicated margin reports** instead of the dashboard panel. Those dedicated reports are delivered by the margin reporting modules that come with this change — the Community `sale_project` addon itself no longer computes the panel.
- Consequently the sales side panel of the project dashboard disappears, and the sales-order-item data that fed it (the `get_sale_items_data` / `_get_sale_items_domain` machinery) is gone from `sale_project`.
- What Community users get directly from `sale_project`: a new **Actual Margin** indicator on the project, and a new **Billable Type** classification on analytic items.
- On the project dashboard, the button formerly named *Dashboard* is renamed *Updates*, and the sales/commercial indicators now belong to the **project form view** rather than the dashboard page.

If you only use Community, treat the above as the complete functional delta: no Enterprise-only margin feature is shipped in this addon.

## Technical data model changes

**Added**

- `account.analytic.line.billable_type` — `Selection`, **stored**, computed (`_compute_project_billable_type`, `compute_sudo`), readonly. Depends on `so_line.product_id` and `amount`. Values: *Revenues Fixed Price*, *Revenues Milestones*, *Revenues Manual*, *Service Revenues*, *Other Revenues*, *Other Costs* (positive amounts/quantities → revenue bucket, otherwise *Other Costs*).
- `project.project.actual_margin` — `Monetary`, computed (`_compute_actual_margin`), **not stored**. Sum of `account.analytic.line.amount` grouped by the project's analytic account.
- `project.project.action_actual_margin()` — new action: opens the analytic reporting window titled "*<Project>*'s Actual Margins", domain restricted to the project's analytic account(s), with fiscal-date and group-by-date defaults.

**Removed / breaking**

- `sale_project` no longer extends `account.move` (the `AccountMove` class and its `_get_action_per_item()` are deleted) and no longer extends `project.update` (`ProjectUpdate._get_template_values(project)` is deleted — the dashboard template no longer embeds sales order items).
- `sale.order.line._get_action_per_item()` removed.
- `project.project` loses the whole profitability/panel API: `action_profitability_items(section_name, domain=None, res_id=False)`, `get_panel_data()`, `get_sale_items_data(offset, limit, with_action, section_id)`, `_get_sale_items_domain`, `_get_domain_from_section_id`, `_show_profitability`, `_show_profitability_helper`, `_get_profitability_labels`, `_get_profitability_sequence_per_invoice_type`, `_get_service_policy_to_invoice_type`, `_get_profitability_sale_order_items_domain`, `_get_revenues_items_from_sol`, `_get_items_from_invoices_domain`, `_get_items_from_invoices`, `_add_invoice_items`, `_get_profitability_items`, `_get_profitability_values`, `_get_foldable_section`, `_get_stat_buttons`.
- Unused imports (`json`, `defaultdict`) dropped from `project_project.py`.
- No other field is removed on `project.project` or `sale.order.line`; `actual_margin` and `billable_type` are purely additive.

**Migration impact:** any custom module overriding the removed methods, or calling them from a report/button, will break at upgrade and must be ported to the new Analytic Items-based flow.

## How your habits should change

- Stop looking for profitability in the project dashboard: open the **project form**, use the **Actual Margin** action to drill down into the underlying analytic items (grouped by date), and use the dedicated margin reports for budget vs actual vs projected margins.
- The dashboard page no longer carries the sales side panel; use the project form for commercial indicators, and the renamed **Updates** button for project updates.
- Analytic lines get a **Billable Type** automatically: time/costs booked against a sales order line on a service product are flagged as *Service Revenues*, others are split between fixed-price, milestones, manual, other revenues and other costs. Expect stored values to be recomputed for existing lines during the upgrade.
- Review customisations and Studio automations that referenced the old profitability methods or the dashboard sale-items data.

## What you gain by migrating

- **One source of truth for margins:** figures come from analytic lines, so the project's actual margin matches analytic accounting instead of a dashboard-specific computation.
