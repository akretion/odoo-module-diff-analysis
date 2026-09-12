# product migration guide (12.0 -> 13.0)

This guide covers the `product` addon (Community edition) when moving from Odoo 12.0 to 13.0.

## What's new for users

- **Product images were reworked.** The main product image is now sized the same way as the extra images, so galleries and thumbnails stay consistent everywhere.
- **Extra images on variants.** You can now attach multiple images to a product *variant*, not only to the template, and variants get their own website URL.
- **Order your media.** Drag & drop extra product media in the backend to sequence them.
- **Clearer price rules.** The "Apply On" choices now read naturally (All Products, Product Category, Product, Product Variant), and fixed prices are displayed with the pricelist currency symbol.
- **Two pricelist levels.** Settings now offer *basic* (multiple prices per product) and *advanced* (discounts, formulas) instead of a set of technical check boxes.

Release-note topics such as payment acquirers (PayU Latam), Amazon sync, rental or other apps are out of scope or Enterprise-only and are not part of this Community addon.

## Technical data model changes

**Attributes and variants rebuilt**
- `product.product.attribute_value_ids` (m2m to `product.attribute.value`) is **removed**. A variant now stores `product_template_attribute_value_ids`: a stored m2m (relation `product_variant_combination`, `ondelete='restrict'`) to `product.template.attribute.value`.
- New `combination_indices` (char, stored, indexed), with a unique index enforcing at most one active variant per combination.
- `price_extra` and variant names are computed from those stored values; `_compute_product_template_attribute_value_ids`, `_check_attribute_value_ids` and `_has_valid_attributes` are gone.

**Attribute lines and values**
- `product.template.attribute.line` gains `active`; lines and values are archived rather than deleted when still referenced (sales orders, variants). Archiving a line clears its values, and a new line reactivates a matching archived one, reusing its variants.
- `product.template.attribute.value` gains `attribute_line_id` (required), `ptav_active` and `ptav_product_variant_ids`; `product_tmpl_id` / `attribute_id` are now stored related fields. New unique constraint on `(attribute_line_id, product_attribute_value_id)`.
- On the line, `product_template_value_ids` changed from a computed m2m to a real one2many.

**Images**
- Templates and variants now inherit `image.mixin` and use `fields.Image`.
- Replaced: `image`, `image_original/big/large/medium/small`, `image_raw_*` → `image_1920`, `image_1024`, `image_512` (new), `image_256`, `image_128`, `image_64`.
- On variants: `image_variant` → `image_variant_max`, plus `image_variant_1024/512/256/128/64` and `can_image_variant_be_zoomed`; the variant still falls back to the template image.

**Pricelists**
- `product.pricelist.item` is now described as "Pricelist Rule"; `fixed_price` became a `Monetary`; `compute_price` is required; new `active` related to the pricelist; new consistency constraint on the "applied on" target.
- `product.template.item_ids` is removed, replaced by a `pricelist_item_count` and an "open price rules" action on templates and variants.
- Settings: `group_pricelist_item` removed; new `product_pricelist_setting` (basic/advanced) drives `group_product_pricelist` / `group_sale_pricelist`.

**Removed model and deprecated API**
- `product.price.history` is removed (with `_set_standard_price` / `get_history_price`): valuation now relies on stock valuation layers.
- Also removed: `valid_archived_variant_ids`, `valid_existing_variant_ids`, `_get_valid_product_*`, `_get_archived_combinations`, `_get_existing_combinations`, `price_get`, `_price_get`, `_price_rule_get_multi`, `_get_partner_pricelist`.

## How your habits should change

- **Images**: the field you wrote to (`image`) is now `image_1920`; sizes are named by pixel dimensions. Add and reorder extra media per variant.
- **Variants**: the value combination is stored on the variant itself, several lines can share the same attribute, and archiving keeps history instead of deleting values.
- **Pricelists**: choose the level in Settings first; keep *basic* if you only need fixed prices per product, switch to *advanced* for discounts and formulas.
- **Any custom report, import or data script** touching cost history, `attribute_value_ids` or the old technical fields must be updated.

## What you gain by migrating

- Consistent, faster images in the backend and on the website, with media per variant.
- A cleaner, more robust attribute model: one active variant per combination, no duplicate combinations, and no data loss when archiving attributes.
- Easier pricelist configuration for non-technical users, with prices shown in the right currency.
