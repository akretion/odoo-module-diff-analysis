# purchase_stock migration guide (18.0 -> 19.0)

## What's new for users

**Group RFQs per vendor.** On the vendor form you can now choose how replenishment needs are grouped into Requests for Quotation — On Order (default), Daily, Weekly or Always — and for weekly grouping pick the weekday on which arrival should be planned.

**Forecast-based purchasing and purchase catalog.** The catalog displays suggested quantities computed from demand history (monthly demand) and lets you consult forecasted quantities. Product cards can show stock availability, and purchase/packaging units are displayed next to the cost in product unit.

**Reordering rules.** A horizon (365 days by default) and a deadline date (latest date to reorder before hitting minimum stock) have been added, together with preview data such as order frequency and average stock. The quantity to order now defaults to the multiple defined on the vendor pricelist (or bill of materials) and can exceed the maximum quantity to avoid under-ordering.

**Product routes.** The Buy route is now set automatically when a vendor is defined (Manufacture when a BoM exists), reducing manual configuration. A missing vendor on an MTO product no longer blocks the flow: the default warehouse route is used instead.

**Inventory valuation.** Simplified, with a new closing interface, periodic closing handling delivered-not-invoiced and received-not-billed quantities, backdating support, and real-time valuation posted at bill/invoice time only. Input and output accounts have been removed.

## Technical data model changes

- `stock.valuation.layer` is removed; value is now stored on `stock.move` and derived from accounting documents (priority: manual > bill/invoice > PO/SO > standard price). Price-difference logic (`_apply_price_difference`, `_generate_price_difference_vals`, `_prepare_pdiff_*`, `_get_valued_in_moves`) disappears from `account.move.line`; `_get_stock_moves`, `_get_value_from_account_move` and `_get_value_from_quotation` are added. `account.move.button_draft` is added.
- Price-difference accounts: `product.template.property_account_creditor_price_difference` and `product.category.property_account_creditor_price_difference_categ` are removed; use `product.category.property_price_difference_account_id`.
- `procurement.group` is replaced by `stock.reference`. `purchase.order.group_id` becomes `reference_ids` (Many2many); `purchase.order.line.group_id` is removed, `orderpoint_id` becomes `ondelete='set null'`, and `_prepare_procurement_values` loses its `group` argument. New `res.partner.group_rfq` / `group_on` fields drive RFQ merging.
- `product.template.route_ids` no longer defaults to the Buy route. A new constraint `_check_buy_route` forbids assigning it to a non-purchasable product. `stock.warehouse.buy_to_resupply` becomes computed/inversed.
- Reordering rules: `_compute_qty`, `_compute_visibility_days`, `_set_default_route_id` and `_compute_show_suppplier` are removed; `_compute_qty_to_order_computed`, `_compute_deadline_date`, `_compute_effective_vendor_id`, `_compute_show_supplier` and `_get_replenishment_multiple_alternative` are added.
- Suggest UX: new `product.product._compute_monthly_demand`, `_compute_suggested_quantity`, `_compute_suggest_estimated_price`, plus `purchase.order.action_purchase_order_suggest` and `retrieve_dashboard` (on-time delivery).

## How your habits should change

- Grouping of RFQs is configured on the vendor, not through procurement groups; purchase orders now carry References rather than a Procurement Group.
- Product routes are mostly automatic: only review the Buy route for special cases, and check horizons, deadlines and multiples on your reordering rules.
- Expect fewer and clearer valuation entries: no input/output accounts, a closing report to justify inventory value, and backdated operations absorbed by closing entries.

## What you gain by migrating

Simpler product setup, replenishment hints based on real demand, vendor-level RFQ grouping, and a leaner, more auditable inventory valuation that no longer requires managing input/output accounts or replaying full FIFO history.
