# point_of_sale migration guide (16.0 -> 17.0)

This guide covers what changes in the Point of Sale app (Community edition) when moving from Odoo 16.0 to 17.0.

## What's new for users

**Combo meals.** You can now sell products grouped as a combo: for example a "Burger Menu" with a drink, a main course and a dessert to choose from. Create the combo product, define the choice groups and their products, and the cashier picks the components at the point of sale. If one component is more expensive, a "Price Extra" (combo price) is added on top of the combo price, and component unit prices are adjusted so the order total matches the combo price. Combo lines are linked to their parent line, are deleted together, and are treated as a single unit when splitting a bill.

**Simpler loading behaviour.** The POS always loads the 20 000 most relevant products and the 100 most relevant partners when a session is opened, with no background loading.

Note on the official 17.0 Point of Sale release notes: the extract we reviewed mostly describes Enterprise modules (preparation display, restaurant floor plans, self-order, kiosk, online payment) and features already available in 16.0 Community. Nothing else there changes the Community `point_of_sale` module; the changes below come from the module's source diff.

## Technical data model changes

**Removed fields on `pos.config`:** `limited_products_loading`, `limited_products_amount`, `product_load_background`, `limited_partners_loading`, `limited_partners_amount`, `partner_load_background`.

**Removed fields on `res.config.settings`** (related settings): `pos_limited_products_loading`, `pos_limited_products_amount`, `pos_product_load_background`, `pos_limited_partners_loading`, `pos_limited_partners_amount`, `pos_partner_load_background`.

The limits are now hardcoded (20 000 products / 100 partners) and `pos.session._get_pos_ui_product_product` and `_get_pos_ui_res_partner` always use the limited queries.

**Renamed field:** `pos.combo.line.price` ("Price Extra") becomes `combo_price`. Stored values need to be carried over during the upgrade.

**New selection value:** `product.template` and `product.product` now accept `combo` in `type` / `detailed_type` (`ondelete='set consu'`).

**New fields on `pos.order.line`:**
- `combo_parent_id` (Many2one to `pos.order.line`) – the parent combo line;
- `combo_line_ids` (One2many, inverse of `combo_parent_id`) – the component lines.

Order lines now export `combo_parent_id` and `combo_line_ids` to the front end, and new helpers (`_link_combo_items` on `pos.order`, `_is_field_accepted` on `pos.order.line`) restore the links when an order is saved.

**New data loaded for the POS:** `pos.combo` and `pos.combo.line`; `product.product` loading now includes `combo_ids`.

**Renamed method:** `PosConfig._get_availlable_product_domain` → `_get_available_product_domain` (typo fix). Any custom module calling or overriding it must be updated.

**Fix:** `PosCombo._compute_num_of_products` now initialises `num_of_products` to 0, avoiding an error when creating a combo without lines.

## How your habits should change

- In Point of Sale settings, the "limited loading" and "load in background" options no longer exist. Don't look for them: loading is automatic and identical for every point of sale.
- Databases with more than 20 000 sellable products or more than 100 customers behave like a 16.0 configuration with limited loading enabled, minus the background completion.
- Combos replace the manual grouping workarounds: create a combo product, define its groups and products, then sell it like any other product and let the cashier choose the components in the popup.
- Custom code: rename calls to `_get_availlable_product_domain`, replace `price` by `combo_price` on combo lines, and expect the new combo fields on order lines.

## What you gain by migrating

- Native combo meals, with prices correctly distributed between components and clean, linked order lines.
- A simpler, more predictable configuration: one loading behaviour instead of several settings that could slow the POS down.
- A richer order line data model (parent/child combo relations), making reporting and custom integrations easier.
- Alignment with the supported Odoo branch, which smooths future upgrades.
