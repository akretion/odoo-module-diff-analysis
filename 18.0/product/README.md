# product migration guide (17.0 -> 18.0)

## What's new for users
- **Simpler product form.** "Storable" and "Consumable" are replaced by **Goods**, and a new **Tracked** field says if and how the product is tracked. The former "Detailed Type" disappears: a single **Product Type** (Goods / Service) remains.
- **Better attribute maintenance.** You can now **delete an attribute value** that was used on products, once you have removed it from those products. New actions let you **add a value to all products** or **push a new extra price everywhere** at once, and unused attributes can be **archived**.
- **Pricelists revamped.** The "Advanced Pricelists" setting is gone: pricelists are enabled or not, and all rule types are available. Rule labels are clearer, with a **Markup** concept beside discount and "Extra Fee" instead of "Price Surcharge". Pricelists can now be **printed/exported in PDF, CSV or XLSX**.
- **Combo products** (pick several products among several options) are now handled by the product module itself and available in Sales and eCommerce.
- **Product documents**: document links are validated when entered.

## Technical data model changes
- product.template: the `detailed_type` field is **removed**; `type` becomes a stored selection (`consu` / `service`) instead of a computed field. `_compute_type`, `_detailed_type_mapping`, `_constrains_detailed_type` and `_sanitize_vals` are removed. Added: `copy_data` (replacing `copy`), `get_contextual_price`, `name_search` + `_search_display_name` (replacing `_name_search`), `_compute_service_tracking`, `_compute_purchase_ok`, `_get_list_price`, `_prepare_tooltip`, `_check_combo_ids_not_empty`, `_check_sale_combo_ids`. `_get_attribute_exclusions` / `_get_own_attribute_exclusions` take a new `combination_ids` argument.
- res.config.settings: `group_sale_pricelist` and `product_pricelist_setting` are **removed**, with their onchange methods.
- product.pricelist.item: new fields `display_applied_on`, `price_markup`, `product_uom`, `product_variant_count`; `_compute_name_and_price` is split into `_compute_name` + `_compute_price_label`; `_check_recursion` becomes `_check_pricelist_recursion`; many new onchange helpers (`_onchange_base`, `_onchange_base_pricelist_id`, `_onchange_price_markup`, `_onchange_validity_period`...).
- product.pricelist: `action_open_pricelist_report` and `copy_data` added; default ordering is now sequence, id, name.
- product.attribute / product.attribute.value: `action_archive`, `action_open_product_template_attribute_lines`, `action_add_to_products`, `action_update_prices`, `check_is_used_on_products`, `unlink`, `write(vals)`.
- Combo logic is moved into this addon: `product.combo` and `product.combo.item` now live in `product`, with new compute methods (`_compute_base_price`, `_compute_combo_item_count`, `_compute_currency_id`) and constraints.
- product.supplierinfo: new `create` / `write` handling variant-template consistency. product.document: `copy` replaced by `copy_data`, plus `_onchange_url`.
- Core renames that impact the addon: `_name_search` → `_search_display_name`, `_search` no longer accepts `access_rights_uid`, group_expand methods no longer receive `order`.

## How your habits should change
- Forget Storable / Consumable / Service: pick **Goods or Service**, then set the **Tracked** field to manage stock.
- There is nothing to configure in the Pricelist settings anymore: no "Advanced Pricelists" toggle. Existing rules keep working, but the discount / markup fields shown depend on the rule's "Based on" value.
- To delete an attribute value that is used, remove it from the products first, otherwise Odoo refuses the deletion.
- If you have Studio fields, custom reports or integrations referring to `detailed_type`, `group_sale_pricelist` or `product_pricelist_setting`, they must be adapted during the migration.

## What you gain by migrating
- One unambiguous product type plus explicit tracking: fewer user mistakes and cleaner product data.
- Much faster variant and attribute maintenance: apply an attribute change to hundreds of products in one action, archive obsolete attributes, delete unused values.
- Rationalised pricelists: a single setting, clearer labels, markup support, and printable/exportable price lists for sales teams and customers.
- Combo products natively supported by the core product module, ready for Sales and eCommerce.
- Staying current also means receiving bug fixes and new features instead of accumulating upgrade debt.
