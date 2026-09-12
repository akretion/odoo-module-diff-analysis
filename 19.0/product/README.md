# product migration guide (18.0 -> 19.0)

The 19.0 version of this addon is largely about **simplifying units of measure**: product packagings are merged into units of measure, and rounding precision is unified.

## What's new for users

- **Packagings merged into units of measure.** The "Product Packaging" concept is gone: to sell a "Box of 12", you create the unit of measure and add it to the product's units. Packaging barcodes now belong to those units. Units of measure categories have been removed.
- **One single rounding precision.** Rounding can no longer be set per unit of measure: it always follows the "Product Unit" decimal accuracy, so the two values can never disagree, and the old precision warning popups are gone.
- **Purchasing.** The purchase unit field is removed from products; a unit is required on vendor price lines, and the cost in the product unit is displayed beside the purchase-unit cost in the purchase catalog.
- **Catalog and configuration.** Products can be organised in order sections, with a filter for the selected section. Extra units/packagings added on a product are selectable in the shop and configurators, and the combo configurator has been improved.
- **Pricelists.** Rules are displayed inline on the product form instead of behind an "Open pricelist rules" button, rules of archived pricelists are hidden, and a rule no longer necessarily needs a pricelist.

## Technical data model changes

- **Removed model:** `product.packaging`. The addon no longer overrides `decimal.precision` or `uom.uom` for rounding.
- **New model `product.uom`** (link between products and their UoMs): `uom_id`, `product_id`, `barcode` (unique), `company_id`; its display name is the barcode.
- `product.product`: `+product_uom_ids`, `+action_archive`/`+action_unarchive`, `+_onchange_standard_price`, `+_update_uom`/`+_trigger_uom_warning`, `+_compute_pricelist_rule_ids`, `+_search_is_in_selected_section_of_order`, `+_get_product_placeholder_filename`; `-packaging_ids`, `-pricelist_item_count`, `-toggle_active`.
- `product.template`: `-packaging_ids` (and its compute/inverse); `+default_get(fields)`, `+_get_available_uoms`, `+_has_multiple_uoms`, `+_compute_is_dynamically_created`, `+_service_tracking_blacklist`, `+_get_product_document_domain`.
- `uom.uom`: `+product_uom_ids`, `+packaging_barcodes_count`, `+action_open_packaging_barcodes`; `rounding` becomes a computed field.
- `product.pricelist.item`: computed/inverse `price_markup` (markup = negative discount); `+_compute_company_id`, `+_compute_currency_id`, `+_compute_is_pricelist_required`; `_compute_price` and `_compute_base_price` now accept `**kwargs`.
- `product.pricelist`: `_compute_price_rule(products, quantity, *, currency=..., uom=..., date=..., compute_price=...)` — these arguments are now keyword-only; `+_base_domain_item_ids`, `+_domain_item_ids`.
- `product.supplierinfo`: `+_compute_price`, `+_compute_product_id`, `+_compute_product_tmpl_id`, `+_compute_product_uom_id`, `+_get_filtered_supplier`; `-_default_product_id`; unit of measure now required.
- `product.category`: the default category is no longer required.
- Signature renames: `name_search(name, domain=...)` (was `args`), `write(vals)` (was `values`), `_filter_to_unlink()`, `_get_product_catalog_record_lines(product_ids, **kwargs)`.
- `res.config.settings`: `group_stock_packaging` removed; `group_uom` relabelled "Units of Measure & Packagings".

## How your habits should change

- Stop creating "product packagings": create the unit of measure and add it to the product's units instead.
- Maintain rounding once, in the "Product Unit" decimal accuracy, not on each unit of measure.
- Adapt product category usage: it is optional, no need to create one just to save a product.
- Configure pricelist rules directly on the product form, and check archived pricelists when a rule seems missing.
- Always set a unit of measure on vendor price lines.

## What you gain by migrating

- One concept (units of measure) replaces two overlapping ones (UoM + packagings): less configuration and fewer inconsistencies between ordered quantities, packagings and stock moves.
- Rounding rules that can no longer contradict the decimal accuracy, hence fewer surprising quantity computations.
- A faster product form and catalog: inline pricelist rules, catalog sections and search, per-company/currency rules without pricelist.
- Cleaner, leaner data (removed packaging model, `pricelist_item_count`, `toggle_active`), which makes future customisations and upgrades easier.
