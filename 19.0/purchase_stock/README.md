# purchase_stock migration guide (18.0 -> 19.0)

## What's new for users

- **Group RFQs per vendor.** Vendors (contacts) now have *Group RFQ* (On Order / Daily / Weekly / Always) and *Week Day* fields in the Purchase tab. Replenishment needs are merged into a single RFQ when their expected arrival matches the rule you chose, instead of relying on the technical "procurement group".
- **Buy and Manufacture routes are set automatically.** A product with a vendor gets the Buy route without manual configuration. A product carrying the Buy route must have "Can be Purchased" enabled, otherwise Odoo blocks the change with a clear message.
- **Simplified inventory valuation.** Valuation is no longer built from stock valuation layers: it derives from accounting documents (bill, order, standard price), with real-time valuation posted at invoice/bill time rather than at receipt/delivery. The closing report handles backdating and rounding issues.

## Technical data model changes

- `purchase.order.group_id` (procurement.group) is replaced by `reference_ids`, a many2many to the new `stock.reference` model (`stock_reference_purchase_rel`). `purchase.order.line.group_id` and `procurement.group.purchase_line_ids` are removed, and `purchase_stock` now extends `stock.reference` with `purchase_ids`.
- `stock.valuation.layer` is removed (the `models/stock_valuation_layer.py` file is dropped); the value now lives on `stock.move`.
- Price difference accounts: `product.template.property_account_creditor_price_difference` and `product.category.property_account_creditor_price_difference_categ` are removed and replaced by `product.category.property_price_difference_account_id`.
- `account.move.line`: price-difference helpers are removed (`_apply_price_difference`, `_generate_price_difference_vals`, `_replay_history`, `_prepare_pdiff_vals`, `_get_valued_in_moves`, `_get_out_and_not_invoiced_qty`); new `_get_stock_moves()` includes purchase moves. `_eligible_for_cogs()` is renamed `_eligible_for_stock_account()`.
- `product.template.route_ids` no longer defaults to the Buy route: `_get_buy_route()` is removed and a `_check_buy_route()` constraint is added.
- `stock.warehouse.buy_to_resupply` becomes computed with an inverse, writing on the Buy route's warehouse list.
- Signature changes: `stock.warehouse.orderpoint._prepare_procurement_values(date, group)` -> `(date)`; `purchase.order._prepare_group_vals()` -> `_prepare_reference_vals()`; `stock.rule` loses `group_propagation_option`. New `res.partner` fields `group_rfq` and `group_on`. `purchase.order.line.orderpoint_id` is now `ondelete='set null'`. New hooks: `_filter_warehouse_routes`, `stock.rule.run()`.

## How your habits should change

- Stop ticking routes by hand: check "Can be Purchased" on the product, the Buy route follows.
- Group RFQs on the vendor form instead of adjusting procurement groups on rules.
- Forget the Stock Interim (Received/Delivered) and input/output accounts: valuation follows the bill; check the closing report for goods received but not billed.
- Set the price difference account on the product *category*.
- The "Procurement Group" field on purchase orders is gone; use References if you need to tie documents together.

## What you gain by migrating

- Less configuration, fewer manual errors: routes and vendor grouping are handled by Odoo.
- Vendor-level RFQ grouping means fewer, cleaner purchase documents.
- Valuation you can justify from recent documents, with a real closing process and backdating support.
