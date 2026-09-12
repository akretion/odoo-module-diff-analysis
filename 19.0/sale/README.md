# sale migration guide (18.0 -> 19.0)

What changes in the **Sales** app (`sale`) between Odoo 18.0 and 19.0, for functional users and for teams preparing the upgrade.

## What's new for users

- **Catalog sections**: build quotations section by section from the product catalog, with search/tags.
- **Optional sections**: a section or sub-section of a quotation can be marked optional; portal customers can choose quantities on those lines. (Optional *products* are replaced by optional sections.)
- **Hide section prices or composition**: a (sub)section can show only its total, hiding line prices or the lines themselves, in reports and in the portal.
- **Combos**: quantities and choices are easier to edit from the backend, and combo line totals now appear in the backend and in reports.
- **Print and mark as sent**: a button prints the quotation and flags it as sent, without emailing it.
- **Delivery date**: only Goods products are considered (services no longer push the expected date).
- **Packagings are merged into Units of Measure**; packaging definitions are now UoM records.
- **Down payment account** is set once per company in Accounting settings instead of per product category.

## Technical data model changes

Renamed fields (check imports, exports, EDI and custom code):
`sale.order.line.product_uom` -> `product_uom_id`, `sale.order.line.tax_id` -> `tax_ids`, `product.pricelist.item.product_uom` -> `product_uom_name`.

Removed:
- model `product.packaging` (with its Sales flag) and the sale order line fields `product_packaging_id` / `product_packaging_qty`;
- blocking warnings: `res.partner.sale_warn`, `product.template.sale_line_warn` (the message fields remain, shown in warning boxes);
- CRM team statistics `quotations_count`, `quotations_amount`, `sales_to_invoice_count`;
- `product.category.property_account_downpayment_categ_id` (replaced by `res.company.downpayment_account_id`);
- `sale.order.line.child_ids`; `parent_id` is no longer stored.

Added:
- `sale.order.preferred_payment_method_line_id`;
- quotation import template support (`get_import_templates`).

Behaviour changes:
- `sale_async_emails` is merged into `sale`: order emails can be queued and sent by a cron.
- `sale.order` inherits `account.document.import.mixin`; `_create_order_from_attachment`, `_extend_with_attachments` and `_get_order_edi_decoder` give way to the generic `_decode_attachment` flow.
- Section logic is reworked around `_get_section_lines`, `_is_line_in_section`, `get_parent_section_line`, `_get_section_totals`.

Method signature changes for custom modules: `_get_product_catalog_record_lines(product_ids, *, section_id=None)`, `_update_order_line_info(product_id, quantity, *, section_id=False, child_field='order_line')`, `_prepare_procurement_values()` (no more `group_id`), `_send_order_notification_mail(mail_template, allow_deferred_sending=True)`, `payment.transaction._compute_reference_prefix(separator, **values)`.

## How your habits should change

- Warnings no longer block. Partner and product sales warnings appear in a warning box and the "block" behaviour is gone: you can always add the line or confirm the order.
- There is no Packaging field on order lines anymore. Create the matching Unit of Measure on the product and select it on the line.
- Configure the Downpayment Account once, on the company (Accounting settings), rather than per product category.
- Sales team kanban no longer shows quotations or amounts to invoice; use list/pivot views with grouping instead.
- On quotations, the new collapse/hide options directly change what the customer sees in the portal and on the PDF.

## What you gain by migrating

- A simpler configuration: packagings folded into units of measure, a single down payment account per company, non-blocking warnings.
- Better quotations: catalog sections, optional sections customers can fill in, hidden prices, combo totals, print-and-send.
- Cleaner data and performance: fewer stored computed fields, no unused team statistics, asynchronous email sending, and a shared document-import engine for orders and invoices.
- Staying on a maintained version, with sales aligned on the 19.0 accounting, UoM and fiscal engines.
