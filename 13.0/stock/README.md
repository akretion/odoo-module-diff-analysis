# stock migration guide (12.0 -> 13.0)

This addon covers inventory, transfers, locations and putaway. Here is what changes for a functional user moving from 12.0 to 13.0.

## What's new for users

- **Inventory adjustments**: counting stock is simpler — no more "filter" selection, and you can adjust a product quantity without building a full inventory adjustment.
- **Putaway rules**: the old "Put Away Strategy" is replaced by readable rules attached to products, product categories and locations.
- **Rescheduling**: choose per stock rule whether a scheduled-date change is propagated to the next operations, and from which delay. The old company-wide setting is gone.
- **Route renamed**: "Make to Order" is now "Replenish on Order" (the product is not always manufactured).
- **Product description on transfers**: shown on transfer lines and adaptable per transfer type (receipt, delivery, internal).
- **Forecasted inventory**: the forecast is available in graph and grid views.

Other release-note items (valuation layers, landed costs on vendor bills, carrier return labels, lot/serial on invoices, picking assignment) belong to other addons or to Enterprise — check them separately.

## Technical data model changes

**Inventory adjustment (`stock.inventory`)**
- Removed: `filter`, `total_qty`, `category_id`, `exhausted`, `inventory_location_id`, and the single-value `location_id`, `product_id`, `package_id`, `partner_id`, `lot_id`.
- Added: `location_ids` and `product_ids` (many2many), `start_empty`; defaults now depend on `company_id`.
- Lines: new `difference_qty`, `inventory_date`, `outdated`, `is_editable`; `theoretical_qty` is no longer stored; "Checked Quantity" becomes "Counted Quantity"; `product_uom_id` is readonly.
- Duplicate-line check now applies only inside the same adjustment; `_action_done` drops its `cancel_backorder` argument; `action_inventory_line_tree` becomes `action_open_inventory_lines`; new `action_view_related_move_lines`.

**Putaway**
- Models `product.putaway` and `stock.fixed.putaway.strat` are removed; new model `stock.putaway.rule` (`product_id`, `category_id`, `location_in_id`, `location_out_id`, `sequence`, `company_id`).
- `stock.location.putaway_strategy_id` becomes `putaway_rule_ids`; `get_putaway_strategy` becomes `_get_putaway_strategy`.

**Rescheduling / picking**
- `res.company.propagation_minimum_delta` and its settings are removed.
- `stock.move` and `stock.rule` gain `propagate_date` and `propagate_date_minimum_delta`.
- `stock.move.move_line_nosuggest_ids` and `stock.picking.type.show_reserved` are removed (one Detailed Operations view).

**Multi-company**
- `check_company` is enforced on many relations (moves, locations, routes, putaway rules, inventory lines...).
- `stock.move.line.company_id` is no longer related; `company_id` added to package levels, putaway rules and lots; `stock.picking.type.company_id` is required and pickings follow their type.
- Changing the company of a location or putaway rule is forbidden.
- Reordering-rule counters on products are computed per active company.

## How your habits should change

- **Inventory adjustment**: pick one or several locations and/or products; leave both empty to count everything. Lines open in their own view — red = quantity outdated, blue = counted differs from on hand, bold = new line. Existing lines can only be edited on the counted quantity; select outdated lines to refresh the on-hand quantity. Only stock managers can validate. Cancelling keeps the lines, so re-starting no longer regenerates them.
- **Putaway**: configure rules from the product, product category or location form instead of choosing a strategy on the location; product rules win over category rules, then priority decides.
- **Rescheduling**: set propagation and minimum delta on the stock rule rather than in Settings.
- **Transfers**: the "Show Reserved" option on operation types is gone.
- **Multi-company**: choose the company when creating a location or a rule; it cannot be changed afterwards.

## What you gain by migrating

A cleaner inventory process: fewer fields to fill, adjustment lines that show at a glance what is outdated or different, and validation restricted to stock managers. Putaway becomes explicit and maintainable per product or category, rescheduling is tuned where it matters (per rule) instead of globally, and multi-company data — moves, counters, rules — is properly isolated, so the information you rely on for replenishment decisions is more trustworthy in 13.0.
