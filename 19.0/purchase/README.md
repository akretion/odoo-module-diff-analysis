# purchase migration guide (18.0 -> 19.0)

What changes for the Purchase app (`purchase`) when moving from Odoo 18.0 to 19.0, Community edition.

## What's new for users

- **Lock replaces the "Done" status.** A confirmed purchase order is now locked/unlocked through a boolean instead of being moved to a Done state.
- **Acknowledge replaces accept/decline.** Vendors reply through a single acknowledgement on the portal; the old accept/decline/reminder confirmation emails disappear.
- **Polite warnings instead of blocking popups.** Partner and product purchase warnings no longer block or pop up; they appear as information boxes on the order form. You write a message; the "no message / warning / blocking" selector is gone.
- **Upload a bill from the order.** Attach a vendor bill directly to the purchase order, whatever the billing policy of the products.
- **Unit price smart update.** A unit price you edited manually is no longer silently recomputed.
- **Purchase and product units.** When you buy in a unit different from the product's, the cost is shown in both units.
- **Purchase catalog.** More dynamic: suggested quantities, forecast for a date range, and support for sections.
- **RFQ dashboard and comparison.** Improved dashboard with a "late" filter and a duplicate-order warning; a purchase comparison action replaces the purchase history.
- **Cancel and delete.** Purchase orders can be cancelled and deleted, handy for test orders.
- **RFQ import template.** A ready-made import template is available.

## Technical data model changes

- **Field rename.** `product_uom` becomes `product_uom_id` on purchase order lines, purchase reports and vendor pricelists. Update imports, exports, filters and custom modules.
- **Packagings merged into units of measure.** The `product.packaging` model is removed. `purchase.order.line.product_packaging_id` and `product_packaging_qty` are gone, as is the packaging `purchase` flag. UoM categories are removed too; available units come from the product's unit list. Existing packagings must be migrated.
- **Lock / acknowledge.** Fields `mail_reminder_confirmed`, `mail_reception_confirmed`, `mail_reception_declined` removed; new `acknowledged` boolean. Methods `button_done`, `confirm_reception_mail`, `decline_reception_mail`, `confirm_reminder_mail` removed; `button_lock`, `action_acknowledge`, `get_acknowledge_url` added.
- **Warnings.** `res.partner.purchase_warn` and `product.template.purchase_line_warn` removed (message text fields kept, now purely informative); the onchange warnings are replaced by computed warning texts.
- **Security lead time removed.** `res.company.po_lead` and the related settings (`use_po_lead`, `po_lead`) are deleted. Use supplier lead times and "days to purchase".
- **Sections.** `purchase.order.line.parent_id` is no longer stored; `collapse_prices`, `collapse_composition` and `child_ids` are removed.
- **Method signatures.** `action_create_invoice(attachment_ids=False)`, `_update_order_line_info(..., section_id=..., child_field=...)`, `_get_product_catalog_record_lines(..., section_id=None)` and `_prepare_purchase_order_line(..., partner_id, ...)` changed. The partner vendor-bill smart button moved to Accounting.
- **Other.** New helpers for duplicate-order detection, purchase comparison, a "late" dashboard filter, and a selected-seller field on lines.

## How your habits should change

- Stop using the Done stage: lock or unlock confirmed orders.
- Nothing blocks you on warnings anymore: read the box, decide, and maintain only the message text.
- Replace any "security lead time" usage with vendor lead times on pricelists.
- Replace product packagings on purchase orders with units of measure defined on the product.
- Vendors acknowledge the order instead of accepting or declining it.
- Prices you typed are kept; re-check the computed price only if you expect an update.

## What you gain by migrating

- A single, clearer order lifecycle (draft, confirmed, locked) instead of a Done state plus accept/decline flags.
- Fewer interruptions: non-blocking warnings, no blocking popup.
- One unit model instead of two: packagings are just units of measure, so buying, selling and stock data stay consistent.
- Faster daily work: dynamic catalog with suggested and forecast quantities, quotation comparison, duplicate detection, bill upload straight from the order.
- Fewer settings to maintain: the purchase security lead time is gone, and supplier lead times drive scheduling.
