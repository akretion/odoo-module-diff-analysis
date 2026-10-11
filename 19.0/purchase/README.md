# purchase migration guide (18.0 -> 19.0)

What changes for the Odoo `purchase` app between 18.0 and 19.0. Community edition only; Enterprise-only features are not listed.

## What's new for users

- **Packagings merged into units of measure.** The Product Packaging model is gone. A packaging is now a UoM record with a name and quantity, added to the product's allowed UoMs.
- **Non-blocking warnings.** Vendor and product warnings are no longer blocking: they appear in a warning box on the order instead of interrupting you and resetting fields.
- **Acknowledgement instead of accept/decline.** Vendors simply acknowledge an RFQ/order from the portal; the accept, decline and confirmed-date replies disappear.
- **Smarter unit price.** The unit price of an order line is no longer recomputed after you edit it manually.
- **Better RFQ and catalog.** Improved RFQ dashboard; a more dynamic purchase catalog with suggested quantities, forecast-based purchasing and the cost shown in the product unit beside the purchase unit.
- **Alternative RFQs.** Create several alternative RFQs at once, using the vendor currency and copying the analytic distribution.
- **Vendor RFQ grouping.** Choose per vendor whether RFQs are grouped by expected arrival date.
- **Cancel and delete purchase orders**, useful for cleaning up test orders.
- **Upload a vendor bill directly from the purchase order**, whatever the product control policy.

## Technical data model changes

- `product_uom` renamed to `product_uom_id` on `purchase.order.line`, `product.supplierinfo` and `purchase.report` (consistency with sale and account lines). XML-RPC, import templates and custom code must use the new name.
- `product.packaging` model removed (and its `purchase` boolean). `product_packaging_id` and `product_packaging_qty` are removed from `purchase.order.line`; `product_qty` is no longer computed from packaging, and `product_uom_id` is computed from the product/vendor.
- Warnings: `product.template.purchase_line_warn` and `res.partner.purchase_warn` removed; the `*_warn_msg` text fields remain. The related onchange handlers were dropped.
- Vendor confirmation: `mail_reminder_confirmed`, `mail_reception_confirmed` and `mail_reception_declined` removed. Reminder logic now uses `acknowledged` and a stored `receipt_reminder_email`. `confirm_reminder_mail`, `_confirm_reception_mail` and `_decline_reception_mail` removed; `get_confirm_url` is kept for backward compatibility and returns the acknowledge URL.
- Order lines: `parent_id` is no longer stored; `child_ids`, `collapse_composition` and `collapse_prices` removed.
- `res.company.po_lead` and the `use_po_lead` setting removed.

## How your habits should change

- Update import/export templates, spreadsheets and integrations referencing `product_uom` or `product_packaging_id`.
- Recreate packagings as UoM records on products instead of packaging records.
- Do not expect warnings to block a vendor or product: read the warning box and decide yourself.
- Vendor emails no longer offer a decline button or a confirmed-date reply.
- Security lead time is gone: rely on vendor delivery lead time, supplier delay and the company "days to purchase" setting.
- Purchase order sections no longer offer "Hide Prices" / "Hide Composition" options.

## What you gain by migrating

- One simplified unit model: fewer concepts, and no more mismatches between units, packagings and quantities.
- Consistent field naming with sales and accounting, making generic automation, EDI and reporting easier.
- Fewer interruptions: non-blocking warnings and a unit price that stays where you put it.
- Better performance on large orders thanks to the non-stored section parent.
- A more helpful purchasing UX: forecast-based suggestions, alternative RFQs and one-click vendor bill upload.
