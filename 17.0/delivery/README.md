# delivery migration guide (16.0 -> 17.0)

## What's new for users

The 17.0 release notes say almost nothing about the Community `delivery` addon. The only carrier-related entries (Sendcloud improvements, the new Shiprocket and Starshipit connectors) are **Enterprise-only** integrations, not part of the Community addon — so nothing there reaches you with this migration.

For a Community user, the practical outcome is: **the delivery pricing engine is unchanged**. Fixed Price and Based on Rules carriers, price rules on weight / volume / weight×volume / price / quantity, free-shipping thresholds, zip-prefix restrictions, margins and the shipping cost added on quotations and delivery orders all behave as in 16.0.

What changes is *where* the Inventory-side shipping features live: they have been extracted from `delivery` into a new companion module, `stock_delivery`. On a normal Sales + Inventory database you get both modules, so the feature set you know is still there — delivery slips, labels, tracking references, package weights, HS codes — simply organized differently underneath.

## Technical data model changes

**New module `stock_delivery`.** The following were removed from `delivery` and now belong to the Inventory-side module:

- `product.template`: `hs_code`, `country_of_origin`
- `stock.move`: `weight` (and the picking-assignment overrides)
- `stock.move.line`: `sale_price`, `destination_country_code`, `carrier_id`
- `stock.package.type`: `shipper_package_code`, `package_carrier_type`
- `stock.picking`: `carrier_id`, `carrier_price`, `carrier_tracking_ref`, `carrier_tracking_url`, `weight`, `weight_bulk`, `shipping_weight`, `package_ids`, `is_return_picking`, `return_label_ids`
- `stock.quant.package`: the package weight fields
- the `stock.return.picking` override
- `delivery_request_objects.py` (`DeliveryPackage`, `DeliveryCommodity`)

**Consolidation inside `delivery.carrier`.** `price_rule_ids` (One2many to `delivery.price.rule`) and the `base_on_rule` delivery type, previously declared in the separate `delivery_grid.py` / `ProviderGrid` class, are now plain members of `delivery.carrier`, together with the `base_on_rule_*` methods. The `delivery.price.rule` model itself is untouched (same fields, same `ondelete='cascade'`).

**Changed field behaviour.**

- `invoice_policy` on the carrier is reduced to `('estimated', 'Estimated cost')` in `delivery`; the "Real cost" option and the logic that rewrites the delivery line after shipping moved to `stock_delivery`.
- `_get_conversion_currencies(..., 'company_to_pricelist')` now converts from the company currency to the **order currency** instead of the pricelist currency. Shipping rates shown in the "Choose a delivery method" wizard can therefore differ when the pricelist currency differs from the order currency.
- `_create_delivery_line` now always writes the computed `price_unit`; the old zero-price "Estimated Cost: …" placeholder and `_format_currency_amount()` are gone.

**Methods no longer on `delivery.carrier` in this module:** `send_shipping`, `get_tracking_link`, `get_return_label`, `cancel_shipment`, `_get_packages_from_order`, `_get_packages_from_picking`, `_get_commodities_from_order`, `_get_commodities_from_stock_move_lines`, `_product_price_to_company_currency`.

Files were also split by model name (`delivery_grid.py` → `delivery_price_rule.py`, `partner.py` → `res_partner.py`, plus new `sale_order_line.py` and `stock_quant_package.py`). No method signature changes were detected.

## How your habits should change

- **Carrier configuration: nothing to redo.** Existing carriers, price rules, zip prefixes and margins are preserved.
- **Customizations and Studio fields** that referenced `stock.picking.carrier_id`, `stock.move.line.sale_price`, `product.template.hs_code`, package weights, etc. must now be pointed at `stock_delivery`; the same applies to any module overriding the shipping methods listed above.
- **Keep Inventory installed** if you use labels, tracking, package weights, HS codes / country of origin, or the "Real cost" invoicing policy — that is what brings `stock_delivery`.
- **Sales-only setups** (no Inventory) will see only the "Estimated cost" invoicing policy on the carrier form.
- **Re-test shipping cost calculation** on the delivery method wizard if you sell in a currency other than your pricelist currency.

## What you gain by migrating

- A supported, security-maintained Odoo version, with the same pricing engine and no data migration on your carriers or price rules.
- A cleaner architecture: the delivery logic for sales and website is now independent of Inventory, which makes lighter, faster databases possible.
