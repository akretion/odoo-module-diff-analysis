# product migration guide (12.0 -> 13.0)

Core catalog addon shared by Sales, Purchase, Inventory, eCommerce and Point of Sale. Version 13.0 rebuilds product images, simplifies pricelists and removes a large amount of legacy code.

## What's new for users

- **Harmonized product images.** The main image is now sized exactly like the extra images, so galleries are consistent. Images are stored in ready-made sizes (1920, 1024, 512, 256, 128, 64 px) instead of being resized on the fly: pages load faster, especially in eCommerce.
- **Extra images on variants.** Extra images were previously limited to the product template; they can now be attached to each product variant.
- **Drag & drop** to reorder the extra media of a product.
- **Simpler pricelists.** Settings offer two levels: *Multiple prices per product* and *Advanced price rules (discounts, formulas)*. Rules are called "Pricelist Rules", read more clearly, and a **Price Rules** smart button on the product/template form shows only the rules that apply to it.
- All of the above is available in the Community edition.

## Technical data model changes

- `product.template` and `product.product` now inherit the new `image.mixin`. Removed `image`, `image_small`, `image_medium` and, on variants, `image_variant`; added `image_1920 / 1024 / 512 / 256 / 128 / 64` and `can_image_1024_be_zoomed`, plus technical `image_variant_max` / `image_variant_*` on variants. Displayed images fall back to the template image when the variant has none; a single-variant product always writes its image on the template.
- Removed model `product.price.history` (`company_id`, `product_id`, `datetime`, `cost`) and the methods `_set_standard_price()` and `get_history_price()`. Standard price changes are no longer logged in `product`: valuation now comes from the stock valuation layers.
- Deprecated API removed: `valid_archived_variant_ids`, `valid_existing_variant_ids`, `price_get()`, `_price_get()`, `_price_rule_get_multi()`, `_get_partner_pricelist()`, `_get_valid_product_template_attribute_lines()`, `_get_valid_product_attributes()`, `_get_valid_product_attribute_values()`, `_get_archived_combinations()`, `_get_existing_combinations()`, `_get_no_variant_product_template_attribute_values()`.
- Pricelists: `pricelist_item_ids` (variant) and `item_ids` (template) are replaced by the integer `pricelist_item_count` and the new `open_pricelist_rules()` action. On `product.pricelist.item` (now "Pricelist Rule"): `fixed_price` is a Monetary field, `compute_price` is required, `applied_on` defaults to `3_global`, a stored `active` follows the pricelist, and new constraints/onchanges validate that the targeted category, product or variant is actually set. Settings: `group_pricelist_item` removed, new `product_pricelist_setting` (basic/advanced) drives `group_sale_pricelist`.
- `product.template.attribute.value` now belongs to its attribute line: new required `attribute_line_id` (cascade), `product_tmpl_id` and `attribute_id` become stored related fields, a uniqueness constraint on (line, value) is added, and the line's `product_template_value_ids` becomes a One2many.

## How your habits should change

- Use the new size-named fields (`image_1024`, `image_128`, …) in your custom views and reports; `image_medium` and `image_small` no longer exist. Storing resized copies means slightly more disk usage.
- Set an image on the variant only when the product really has several variants; otherwise it is still stored on the template.
- Choose the pricelist mode in Settings. Simple prices per product no longer require advanced rules, and rules are reached from the product form smart button rather than the old pricelist-item list.
- Cost-history reports built on `product.price.history` must be rebuilt on valuation layers (Inventory).
- Any customization calling the removed methods or reading `valid_*_variant_ids` must be adapted.

## What you gain by migrating

- Faster, lighter pages thanks to stored, pre-resized images.
- Consistent product visuals and richer variants, each with its own image gallery.
- A clearer, more discoverable pricelist configuration with fewer confusing technical fields.
- A validated attribute-value model (database constraints) that protects your data.
- Less technical debt: legacy v10 fields and methods are gone, and cost valuation is aligned with Odoo's supported valuation layers.
