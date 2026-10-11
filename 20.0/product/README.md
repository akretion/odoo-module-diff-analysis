# product migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes cover application-level features (eCommerce, Point of Sale, Sales, Inventory, Manufacturing, localizations). They contain **no dedicated section for the core `product` addon**, so the user-visible changes below come from the 20.0 source changes themselves.

- **Variant setup has been overhauled.** Variants are now managed directly from the variants **list** instead of opening each variant form.
- **Attribute exclusions only apply inside a single product.** Previously you could exclude values for optional/accessory products using a separate "exclusion" record. That is removed: you now simply flag, on one value, which other values of the *same* product it is incompatible with.
- **Extra price visibility per value.** A new "Show Extra Price" flag on each attribute value lets you show or hide the extra price separately (e.g. on the shop page) for specific values.
- **Display order of values.** Attribute values now carry a sequence, so you control the order they are displayed in.
- **New product import with variant grouping.** One file can create/update a whole product family: rows sharing the same product Name are grouped under one template as its variants, and attributes/values are created automatically when missing.

## Technical data model changes

From the 19.0 -> 20.0 diff of `addons/product`:

**Removed**
- Model `product.template.attribute.exclusion` (file `product_template_attribute_exclusion.py` deleted).
- Field `product.template.attribute.value.exclude_for` (One2many).
- Method `_get_parent_attribute_exclusions()` on `product.template`.

**Added / changed fields**
- `product.template.attribute.value.excluded_value_ids` — Many2many (relation `product_template_attribute_excluded_value_ids_rel`), replacing `exclude_for`; domain limited to the same template, another attribute line and active values.
- `product.template.attribute.value.show_price_extra` — Boolean.
- `product.template.attribute.value.sequence` — Integer; `_order` becomes `sequence, attribute_line_id, product_attribute_value_id, id`.
- `import_attribute_values` — non-stored Char on both `product.template` and `product.product` (computed + inverse; import only), used by the new grouping import.
- `action_edit_product_attribute_value()` — opens the attribute value form (replaces the removed form view in `action_open_attribute_values`).

**Method signature changes** (parent-product combinations are no longer supported)
- `_is_variant_possible(parent_combination)` -> `_is_variant_possible()`
- `_get_possible_variants(parent_combination)` -> `_get_possible_variants()`
- `_get_attribute_exclusions(parent_combination, parent_name, combination_ids)` -> `_get_attribute_exclusions(combination_ids=None)`; the returned dict no longer contains `parent_exclusions`, `parent_combination`, `parent_product_name`
- `_get_mapped_attribute_names(parent_combination)` -> `_get_mapped_attribute_names()`
- `_is_combination_possible(combination, parent_combination, ignore_no_variant)` -> `_is_combination_possible(combination, ignore_no_variant=False)`
- `_get_first_possible_combination(parent_combination, necessary_values)` -> `(necessary_values=None)`
- `_get_possible_combinations(parent_combination, necessary_values)` -> `(necessary_values=None)`
- `_cartesian_product(..., parent_combination)` -> `_cartesian_product(...)`

**Other behaviour**
- Writing `excluded_value_ids` (instead of `exclude_for`) triggers variant regeneration.
- `product.product.unlink()` short-circuits when context `create_product_product=False` (used by the import).
- Import template renamed: `/product/static/xls/product_template.xls` -> `product_product.xls`.

## How your habits should change

- **Exclusions:** to make two variants incompatible, edit the value on the product itself and set "Exclude For". Cross-product exclusions (optional product vs. its parent) are no longer supported and must be handled differently.
- **Variant work:** use the variants list rather than each variant form.
- **Imports:** if your file uses the attribute-values column, respect the `Attribute:Value,Attribute:Value` format. External IDs must be `product.product` IDs when attribute values are set, and `product.template` IDs otherwise. Re-importing an existing record fails if its attribute values differ from the file. Re-download the new `.xls` template.
- **Customisations:** any custom/Studio code calling the methods above must be adapted, as the `parent_combination` arguments disappeared.

## What you gain by migrating

- **Faster variant setup:** variants handled from a list, simpler exclusion rules, fewer clicks and less risk of inconsistent configurations.
