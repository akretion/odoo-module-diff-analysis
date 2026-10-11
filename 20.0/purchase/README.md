# purchase migration guide (19.0 -> 20.0)

## What's new for users

* **Product catalog:** when you purchase in a unit or packaging different from the product's unit, the cost in the product unit is displayed next to the purchase-unit cost.
* **Default Incoterm per vendor:** set it on the vendor form and it is applied automatically on every quotation and purchase order of that vendor.
* **Expected arrival date:** edit it directly on the purchase order while the original date given by the vendor is kept for on-time delivery reporting. If the deadline is already past, recompute the new expected arrival dates in one click.
* **Sections and totals:** sections and notes can now be added in purchase agreements/blanket orders, and sections and subsections display total amounts.
* **Alternatives comparison** of RFQs has been improved to make choosing the best offer simpler.
* **Vendor portal:** the customer address is shown on purchase orders.
* **Vendor bill / purchase order matching:** the "Auto-Complete" option checks for matching bill lines before creating new ones, a summary of what was matched is shown, a warning appears when price or quantity differs, and a match can be undone.
* **Intercompany:** synchronized vendor bills are automatically matched with synchronized purchase orders.
* Receipts now display the vendor purchase reference.

## Technical data model changes

* `purchase.order.line` now inherits the new `product.catalog.line.mixin`, which formalises the product catalog API shared by purchase, sales and accounting.
* New `label` field on `purchase.order.line`: computed (non-stored) Text with an inverse, combining the product display name and the description. Reports, portal and list search use it.
* `name` (Description) is no longer required and stores only the description; the product display name is no longer included in it.
* Removed field: `translated_product_name` (and its compute method).
* Removed methods: `PurchaseOrder._default_order_line_values`, `_update_order_line_info`, `_get_default_create_section_values`, `_get_parent_field_on_child_model`, and `PurchaseOrderLine._get_product_catalog_lines_data`.
* New or changed hooks: `_get_catalog_currency`, `_get_product_price_type`, `_get_product_catalog_uom_data`, `_catalog_create_new_line`, `_get_product_catalog_default_unit_price`, `_has_sections`, `_get_quantity_field`, `_get_product_uom_field`, `_get_catalog_unit_price`, `_can_be_unlinked_from_catalog`, `_update_catalog_quantity`, `_get_product_display_name`, `_compute_label`, `_inverse_label`.
* Signature changes: `_is_readonly()` now calls `super()` and also returns True for cancelled orders; `_get_product_catalog_product_data(product, **kwargs)`; `_get_product_catalog_seller_data(product, *, uom=None, **kwargs)` (the old `uom_id` keyword was renamed).
* Catalog context: `product_catalog_currency_id` and `show_sections` are gone, `partner_id` is passed instead; the currency comes from `_get_catalog_currency`.
* `_get_product_purchase_description` returns only the purchase description and no-variant attribute lines; `_prepare_account_move_line` copies the description as is; vendor bill line matching compares `label` instead of `name`.

## How your habits should change

* **Descriptions:** the Description field on a purchase order line now contains only your text; the product name is shown next to it through the new Label field on forms, PDFs and the portal. Editing the Label rewrites the description without the product name prefix.
* Descriptions you typed are no longer overwritten when the vendor or its price list changes; vendor-specific product naming is delegated to the product display name.
* **EDI / e-invoicing:** line descriptions sent to suppliers now contain only the real description, without the product name.
* **Catalog:** adding products from the catalog works as before, but currency, read-only state, sections and units of measure are now handled by the shared mixin. Cancelled orders stay read-only; catalog lines can only be removed while the order is in draft or sent.
* Vendor bill lines are matched to purchase order lines using the new Label, so keep descriptions meaningful.

## What you gain by migrating

* Cleaner, trustworthy line descriptions: an empty description really means empty, and product names no longer leak into vendor files.
* A single shared catalog engine: sections, quantities, units of measure and vendor prices behave consistently across purchase orders and are easier to extend.
* Fewer manual checks: improved bill/PO matching (auto-complete, difference warnings, unmatch) plus automatic intercompany matching.
