# sale_timesheet migration guide (19.0 -> 20.0)

## What's new for users

The 20.0 release notes highlight **Services & Materials** (Sales section: *"Use fixed-price and cost-based services and materials for re-invoicing."*). This is the change actually shipped by this addon:

- A new **Services & Materials** view lets you add extra delivered flat-fee services or consumable materials directly from a sales order, without going through a timesheet.
- Analytic lines created that way are automatically linked to a sale order line, and conversely: Odoo creates or updates the related sale order line according to the product's expense policy (*cost* creates a new line, *sales price* updates an existing one).
- Delivered quantities are updated when those lines change, and the generated sale order line is removed when the analytic line is deleted.
- The delivered quantity of a sale order line is now the sum of upselling analytic lines, stock moves (consumables) and recorded timesheet hours.
- Products with reinvoicing policy *No* stay manual-delivery only; every other policy makes the product eligible for upselling.
- If a project is set on the sales order, the analytic account is deduced from that project; otherwise an analytic account is created on the dedicated sale-order analytic plan.
- Invoices now have a stat button to open the analytic lines (timesheets and others) linked to that invoice.

Note: the other timesheet items in the 20.0 release notes (ActivityWatch, top-menu timer, Timesheet Assistant sharing rules, side activities, thresholds) belong to other Odoo apps and are **not** part of this Community addon.

## Technical data model changes

**account.analytic.line**
- `timesheet_invoice_id` is renamed **`reinvoice_move_id`** (Many2one `account.move`, readonly, no copy, `btree_not_null`), because the link is no longer timesheet-specific.
- `so_line` loses its module-level compute/domain; it is now defined by the sale base module and only customized here (falsy label *Non-billable*, help text). `_domain_so_line` extends `super()`.
- `order_id` is no longer a stored related field on `so_line.order_id`; it is a compute field (`_compute_order_id`, depends on `so_line`, refreshed for lines with a project).
- `_compute_so_line` now calls `super()` and ignores lines without a project.
- Write/delete protection is refactored: `_check_can_write` and the `_unlink_except_invoiced` hook are replaced by `_restricted_fields_when_invoiced()` (adds `employee_id`, `project_id`, `task_id`) plus `_get_invoiced_line_write_error()` / `_get_invoiced_line_delete_error()`.
- Merge rules, partner/project recomputation and the invoice action depend on `reinvoice_move_id.state`.
- `product_uom_id` is now computed.

**account.move**
- `timesheet_ids` is filtered to `project_id != False` (real timesheets only); counts and totals read `reinvoice_move_id`.
- `_link_timesheets_to_invoice` and `_get_range_dates` are removed. Date-filtered linking is now done by `_analytic_line_domain_get_invoiced_lines(so_lines)`, which reads `timesheet_start_date` / `timesheet_end_date` from the context.
- Credit note posting, reversals and move-line deletion clear `reinvoice_move_id`.
- New `_domain_services_analytic_line()` keeps timesheets out of the Services & Materials view.

**account.move.line**: `_timesheet_domain_get_invoiced_lines` is removed (logic centralized in sale).

**sale.order.line**: the `_compute_qty_delivered_method` override (returning `timesheet`) is removed; new hooks `_get_delivered_quantity_by_analytic_domain()` and `_is_line_reinvoicable()`; delivered quantities are aggregated additively (`+=`) instead of assigned.

## How your habits should change

- Any saved filter, report, automation or import using **`timesheet_invoice_id`** must be switched to **`reinvoice_move_id`**.
- Do not rely on `order_id` as a plain related field on analytic lines; it is computed from the sales order line.
- Invoiced timesheets can still not be edited or deleted, but the restriction is now centralized: employee, project and task changes are blocked too, with updated error messages.
- Invoicing a sales order still links timesheets for the wizard's date range; behaviour is unchanged, the date range simply travels in the context.
- When you need to bill an extra service or material, use the new **Services & Materials** view instead of creating a manual analytic line; let Odoo create/update the sale order line for you.

## What you gain by migrating

- A single, shared reinvoicing engine for timesheets **and** other delivered services/materials, so fewer custom developments.
- Reliable delivered quantities: upsell analytic lines, stock moves and timesheet hours are summed instead of overwriting each other.
