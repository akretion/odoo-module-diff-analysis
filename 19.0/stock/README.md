# stock migration guide (18.0 -> 19.0)

This guide covers the `stock` addon (Community edition) between Odoo 18.0 and 19.0. It focuses on what changes for daily users and on the data model impacts of the migration.

## What's new for users

- **Units of measure and packagings are merged** into one simplified model. UoM categories are gone: you are no longer forced to use a UoM of the same category as the product's base unit (e.g. buy in pieces, sell in kilograms). What used to be "packagings" are now simply UoMs listed on the product form ("Packagings") and selectable on eCommerce.
- **Packages can contain other packages** (pack in pack), with package destination handling and improved shipping weight computation.
- **Product quantity on hand is editable directly on the product form**, replacing the separate "update quantity" wizard.
- **Lots / serial numbers**: you can define product-specific lot/serial number sequences; the Lot/Serial form and navigation have been reworked; serial numbers can be filtered by customer; a smart button on the contact form lists the serial numbers linked to that partner; the traceability report shows real vendor/customer names plus the warehouse short code.
- **Reordering rules**: a horizon (365 days by default), a new deadline date, and data previews (order frequency, average stock). The default multiple now comes from the vendor pricelist or the BoM unit, and the ordered quantity may exceed the maximum. The replenishment view shows default values (route, vendor) in previously empty columns.
- **Replenish on Order (MTO)** is now activated through a dedicated setting.
- **Product routes** are set automatically when possible (Buy for purchased products, Manufacture for products with a BoM). An MTO product without vendor falls back to the warehouse default route instead of blocking sales.
- **Location form simplified**: type labels renamed (Vendor, Virtual, Internal, Customer, Transit), the Current Stock button and Comment field removed, and unnecessary virtual locations are no longer created by default.
- **Reports**: Picking Operation and Delivery Slip reports improved; reservation is triggered immediately after an inventory adjustment validation.

## Technical data model changes

- `product.packaging` **model removed**, merged into `uom.uom`. `uom.uom` gains `package_type_id` and `route_ids`, and loses `uom_type`, `color`, `factor_inv`, `ratio`, `category_id`; new `relative_uom_id`, `relative_factor`, `factor`.
- New `product.template.uom_ids` ("Packagings"). On `stock.move`, `product_packaging_id`, `product_packaging_qty` and `product_packaging_quantity` are replaced by `packaging_uom_id` and `packaging_uom_qty`; `product_packaging_qty` is also removed from `stock.move.line`.
- `stock.route.packaging_selectable` renamed to `package_type_selectable`, `packaging_ids` removed; `stock.package.type.route_ids` added.
- UoM category filtering dropped: `product_uom_category_id` removed from `stock.move`, `stock.move.line`, `stock.scrap`, replaced by a computed `allowed_uom_ids` (products' UoMs + vendor UoMs). Changing a product's UoM is now permitted with a warning instead of a blocking error; `stock.warehouse.orderpoint._check_product_uom` and `stock.move._check_uom` constraints removed.
- `stock.quant.package` **renamed to `stock.package`** with multi-level package support; the `stock.package_level` model is removed. Many new computed fields/methods (contained quants, outermost package, picking ids, packing history, `action_put_in_pack`, `action_add_to_picking`...).
- `stock.lot.last_delivery_partner_id` replaced by `partner_ids` (M2M with search); `res.partner.action_view_stock_lots` replaced by `action_view_stock_serial`.
- `stock.location`: `posx`, `posy`, `posz` and `comment` fields removed; `usage` labels changed (View → Virtual).
- Orderpoints: `visibility_days` replaced by `horizon_days` (`get_horizon_days`), `qty_multiple` replaced by a replenishment UoM, new `deadline_date`.
- Settings: FedEx/UPS/USPS connectors are now installed from the `_rest` modules; new `replenish_on_order` setting.
- Technical/ORM: `write(self, values)` → `write(self, vals)`, `default_get(self, fields_list)` → `default_get(self, fields)`; `toggle_active()` deprecated; `action_put_in_pack` uses a keyword-only signature; new put-in-pack hooks; `_action_confirm` gains a `create_proc` argument.

## How your habits should change

- Stop creating packagings: create a UoM with the desired quantity and add it to the product's Packagings field.
- Review reports, labels and delivery documents that referenced packagings or package levels.
- Check that your product UoMs remain consistent — the guardrails are gone, so mistakes are no longer blocked for you.
