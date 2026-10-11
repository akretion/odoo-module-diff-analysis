# sale migration guide (18.0 -> 19.0)

The Sales addon changes mostly under the hood in 19.0, but several field names and a few day-to-day behaviours change. Here is what users will see and what to review before migrating.

## What's new for users

- **Packagings merged with units of measure.** Product packagings are no longer a separate concept: define an extra unit of measure on the product and select it on the order line. Extra units are also selectable on the eCommerce product page.
- **Catalog sections.** Create and manage order sections directly in the catalog, and search products by tags.
- **Hide section prices and composition.** (Sub)section lines can hide their prices (showing only the total) or hide their whole content, in reports and in the portal.
- **Optional sections in the portal.** Sections and subsections of a quotation can be flagged as optional, and portal users can set the quantity of those lines themselves.
- **Combo products.** Combo quantities and choices can now be modified from the backend.
- **Expected delivery date.** It is now computed from Goods products only; services no longer push the delivery date.
- **Print a quotation without sending it.** A button lets you print and mark a quotation as sent without sending the email.
- **Down payment account.** The Down payment Account is no longer set per product category; it is a company-level setting in Accounting settings.

## Technical data model changes

**sale.order.line**
- `product_uom` renamed `product_uom_id` (also purchase.order.line, sale.report, product.supplierinfo); compute `_compute_product_uom` → `_compute_product_uom_id`.
- `tax_id` renamed `tax_ids`, compute `_compute_tax_id` → `_compute_tax_ids`, aligning with account.move.line.
- `product_packaging_id` and `product_packaging_qty` removed, with their computes and onchange; the `product.packaging` model and its `sales` field are gone.
- `parent_id` (parent section) is no longer stored and the `child_ids` One2many is removed; totals now rely on `_get_section_lines` / `_is_line_in_section`. `collapse_prices` and `collapse_composition` now default to False.

**Warnings**
- `res.partner.sale_warn` and `product.template.sale_line_warn` are removed; the message fields `sale_warn_msg` / `sale_line_warn_msg` remain. The blocking warning
