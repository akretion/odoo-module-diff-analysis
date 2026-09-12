# product migration guide (19.0 -> 20.0)

Between 19.0 and 20.0 the `product` addon is mostly about **simpler variant setup**, a **rebuilt product import**, and a **cleaned-up pricing/UoM engine**. No addon-specific extract of the official release notes was available for `product`, so this guide is built on the actual code changes shipped in the addon.

## What's new for users

- **Variant setup overhaul.** Variants can now be managed directly from the list instead of a dedicated form. Attribute-value exclusions have been simplified: you can only exclude values on the *same* product (optional/accessory cross-product exclusions are gone). A new **"Show Extra Price"** flag per attribute value lets you display the extra price separately on the shop page.
- **New product import.** A single file can now create/update templates *and* their variants: rows sharing the same product name are grouped under one template, and attribute values are written as `Color:Red,Size:M`. Attributes and values are created automatically when missing. A new import template (`product_product.xls`) is provided.
- **Stock quantities without the Inventory app.** Incoming, outgoing, forecasted and on-hand quantities are now computed and searchable from the product itself.
- **Packaging barcodes** can be managed directly from the product form.
- **Product categories** can be restricted per company, and translated category names are searchable.
- **Product documents** improvements (attribute values are computed on documents).
- **Pricelist quality fixes**: vendor pricelist date ranges are validated, rules are no longer lost on save, and the sales price of a single-variant product stays in sync with its template.
- **Contacts** now track whether a customer pricelist was set manually.

## Technical data model changes

- **Removed model**: `product.template.attribute.exclusion`.
- `product.template.attribute.value`: `exclude_for` (One2many) → `excluded_value_ids` (Many2many, relation `product_template_attribute_excluded_value_ids_rel`); new `sequence` and `show_price_extra` fields; `_order` now starts with `sequence`; new `action_edit_product_attribute_value`.
- **Variant API cleanup**: `parent_combination` parameters removed from `_is_variant_possible`, `_get_possible_variants`, `_get_attribute_exclusions`, `_get_mapped_attribute_names`, `_is_combination_possible`, `_cartesian_product`, `_get_possible_combinations`, `_get_first_possible_combination`; `_default_order_line_values` removed; `_compute_template_field_from_variant_field` and `_set_product_variant_field` gained extra arguments.
- **Catalog**: `_update_order_line_info(product, quantity, uom, child_field)`, `_get_product_catalog_record_lines(product_ids, child_field)`, plus many new hooks (`_get_product_uom`, `_update_catalog_quantity`, `_get_catalog_unit_price`, `_get_updated_order_line_info`, …).
- **Pricelists**: `_get_applicable_rules(products, quantity, date, uom=)`, `_get_applicable_rules_domain(..., date, quantity, uom)`, `_compute_base_price` (keyword-only `currency`, `date`, `depth`), `_compute_price(product, quantity, uom)`, `_is_applicable_for(product, quantity, uom=)`; `_base_domain_item_ids` and `copy_data` removed; new `_resolve_applicable_rule_and_price`, `_get_related_uoms`, `_get_country_pricelist_multi`, `_check_base_pricelist_id`, `_compute_allowed_uom_ids`, `_get_price_label_base_str`. Rules can now target packagings/UoMs.
- **UoM**: `_get_default_uom_id` → `_default_uom_id`; `_compute_product_uom_id` → `_compute_uom_id`; `product.uom._compute_display_name` removed; new `_get_main_uom`, `_has_multiple_uoms`, `_get_available_uoms`.
- **Quantities**: new `_compute_quantities`, `_compute_quantities_dict`, `_set_qty_available`, `_search_qty_available/incoming_qty/outgoing_qty/virtual_available`, `_compute_is_storable`, `_compute_show_qty_update_button`, `_compute_forecasted_without_stock`.
- **Import**: non-stored `import_attribute_values` field on template and variant; overridden `load()`, `_load_records_create/write`.
- **Base unit model** moved from Point of Sale into `product` (`_compute_base_unit_*`, `_set_base_unit_*`, `_check_base_unit_count`).
- `product.category`: `_check_category_recursion` removed (unified in `base`), new `_check_company_id`, `_search_complete_name`. `product.supplierinfo`: new `_check_dates`, `_get_seller_info`. `copy_data` overrides removed on template/tag/pricelist. `_search_is_in_selected_section_of_order` moved to `account`; `_search_is_in_catalog_order` added.

## How your habits should change

- Configure exclusions **on the attribute value itself** ("Exclude For"), and only within the same product. If you relied on exclusions inherited from an optional/accessory product, that behaviour no longer exists.
