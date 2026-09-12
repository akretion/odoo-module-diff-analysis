# sale migration guide (12.0 -> 13.0)

Covers the Community `sale` addon. 13.0 is a major release for Sales: the accounting engine was rewritten, sales teams simplified and several settings moved.

## What's new for users
- **Sign then pay**: sign a quotation and pay it in one uninterrupted flow; new signature widget.
- **Order date**: order and confirmation dates merged into one smart field.
- **New sales order form**: clearer order lines and column labels.
- **Invoicing**: better wording; the down-payment deduction option only appears when needed.
- **Refunds**: the refunded invoice line is visible on the sale order.
- **Product configurator**: one button adds standard or customizable products to a sales order.
- **Variants**: several images per variant.
- **Pricelists**: add more prices starting from the sales price.
- **Optional products**: reset when the quotation template changes.
- **Delivery**: better carrier selection and delivery cost setting.
- **Emails**: confirmation emails tailored per channel (quotations, eCommerce).
- **Wire transfer**: suggested payment communication defaults to the order/invoice number.
- **Sales teams**: simplified sales team configuration.

## Technical data model changes
(from the addon code diff)

**Accounting merge.** `account.invoice` / `account.invoice.line` no longer exist; they are merged into `account.move` / `account.move.line`.
- `sale.order.invoice_ids` now points to `account.move`; `sale.order.line.invoice_lines` to `account.move.line`.
- Invoice line renames: `invoice_id`→`move_id`, `uom_id`→`product_uom_id`, `invoice_line_tax_ids`→`tax_ids`, `account_analytic_id`→`analytic_account_id`.
- `_prepare_invoice_line(qty)` → `_prepare_invoice_line()`; `invoice_line_create()` / `invoice_line_create_vals()` removed; `_create_invoices()` rewritten.
- Invoice `comment`→`narration`; states `open`/`in_payment`/`paid` replaced by `posted` plus `invoice_payment_state`.

**Terms & conditions.** `res.company.sale_note` and the `sale.use_sale_note` setting are removed; the default note now uses `res.company.invoice_terms` (`account.use_invoice_terms`), configured in Accounting.

**Sales teams.** On `crm.team`, `use_quotations`, `use_invoices` and the sale entries of `dashboard_graph_model` are removed, along with `_onchange_team_type`. Dashboard graph, dates, filters and button are now driven by the `in_sales_app` context; the dashboard button opens the Sales Analysis report.

**Product configurator.** `optional_product_ids` and related code moved out of `sale` into the new `sale_product_configurator` module.

**Pricelists.** `multi_sales_price`, `multi_sales_price_method` and `sale_pricelist_setting` settings are removed; pricelists are now enabled through basic/advanced groups (`product.group_sale_pricelist`).

**Variants.** On `product.attribute.custom.value`, `attribute_value_id` is replaced by the required `custom_product_template_attribute_value_id` (unique per sales order line). On `sale.order.line`, fields are relabelled "Custom Values" and "Extra Values" (deletion restricted).

## How your habits should change
- Invoices are journal entries: same object, same menus. A draft invoice is a real draft journal entry you can edit. Status is split between document state (Draft/Posted/Cancelled) and payment status.
- Set your Terms & Conditions in Accounting, not Sales; it also applies to invoices created without a sale order.
- Sales team setup is much shorter: no team type, no quotation/invoice checkboxes, no graph settings. The dashboard you get depends on the app you open it from.
- Choose between basic and advanced pricelists instead of "fixed / percentage / formula".
- Custom attribute values on order lines are stored against template attribute values; one custom value per attribute value per line.
- Signature is no longer skipped for website sales teams.

## What you gain by migrating
- One coherent accounting engine: sale orders, invoices and journal entries stay consistent, and draft documents are reportable.
- Faster quotation-to-payment: sign and pay in one flow, unified dates, cleaner order form.
- Less configuration noise: simplified sales teams, clearer pricelist settings.
- Better follow-up: refunds visible on the sale order, down payments handled only when relevant.
- More robust variants: attributes and variants archive cleanly, custom values stay on the lines.
