# product migration guide (17.0 -> 18.0)

This guide covers the `product` addon (catalog, variants, attributes, pricelists) between Odoo 17.0 and 18.0.

## What's new for users

**Pricelists simplified.** The old two-level setting ("Multiple prices per product" vs "Advanced price rules (discounts, formulas)") is gone. Pricelists are simply enabled or not, and every rule type (fixed price, discount, formula) is available immediately — no hidden mode to activate. The rule display has been revamped: you choose the rule scope with a simple Product / Category selector, and rules based on a product now show the product name directly. Pricelists can also be generated in PDF, CSV or XLSX format.

**Markup on cost-based rules.** When a rule is based on cost, you can enter a Markup instead of a discount. "Price Surcharge" is renamed "Extra Fee" and the rounding help has been reworded.

**A single product type.** Products are either Goods or Service. The extra internal "detailed type" is removed, so a product can no longer have a type that doesn't match its detailed type.

**Attribute housekeeping.** Attribute values that are no longer used can be deleted (once detached from the products using them), and adding an attribute value or changing its extra cost can be applied to all existing products.

## Technical data model changes

`res.config.settings`
- Removed `group_sale_pricelist` ("Advanced Pricelists").
- Removed `product_pricelist_setting` ("Pricelists Method", config parameter `product.product_pricelist_setting`) and its onchange. Pricelist availability now depends only on `group_product_pricelist`.

`product.pricelist`
- Default order changed from `sequence asc, id asc` to `sequence, id, name`.

`product.pricelist.item`
- Added `display_applied_on` (Product / Category, default Product) driving the new scope selector.
- Added `price_markup` (Float, default 0) and the related fields `product_uom` and `product_variant_count`.
- `product_id` is now restricted by a domain to the selected template.
- Relabelled: `categ_id` "Product Category" → "Category", `product_id` "Product Variant" → "Variant", `price_surcharge` "Price Surcharge" → "Extra Fee".
- `compute_price` selection order changed (percentage, formula, fixed); default remains fixed.
- New helpers `_get_integer`, `_get_displayed_discount`; new onchanges `_onchange_base`, `_onchange_display_applied_on`, `_onchange_price_markup`, `_onchange_validity_period`. `_onchange_rule_content` no longer tests the removed "advanced pricelist" group.

`product.template` / `product.product`
- `detailed_type` removed.
- `type` is no longer computed from `detailed_type`; it is a plain required stored Selection (Goods `consu`, Service `service`). Stock behaviour is driven by the Inventory fields instead.
- Removed `_detailed_type_mapping`, `_compute_type`, `_constrains_detailed_type` and `_sanitize_vals`; `create`/`write` no longer keep `type` and `detailed_type` in sync.

Any custom field, filter, report or import/export using `detailed_type`, `group_sale_pricelist` or `product_pricelist_setting` must be updated. Odoo's upgrade scripts migrate existing data.

## How your habits should change

- In Settings, forget the "basic vs advanced" pricelist question: enable Pricelists and use the rule types you need.
- When creating a rule, first pick Product or Category, then the template, variant or category. Rules are sorted by sequence, then name.
- Price from cost with Markup (it fills the discount automatically); keep using Discount for a reduction.
- On products, set only the Type (Goods or Service) and configure tracking in Inventory.
- Delete an attribute value only after detaching it from every product using it.

## What you gain by migrating

- Simpler, more consistent pricelists: no hidden mode, clearer rule names, markup support, print/export formats.
- A cleaner product model: one Type field, no more type/detailed-type mismatch, simpler imports and integrations.
- Better attribute management: remove unused values and push attribute changes to all products at once.
- A supported version with deprecated fields removed, so customisations no longer drift from the standard data model.
