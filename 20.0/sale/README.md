# sale migration guide (19.0 -> 20.0)

## What's new for users

- **Services & Materials**: a new view (plus a stat button on invoices) lets you add extra delivered services or materials directly from a sale order. Odoo creates the analytic lines and the matching order lines, updates delivered quantities, and cleans up when an analytic line is deleted. The product's re-invoicing policy decides whether a new line is created at cost or an existing one at sales price is updated.
- **Description-only order lines**: lines no longer require a product. The description field is now searchable (products included), and a new "Mandatory Product" setting restores the product-first behaviour. Productless lines receive the company's default sales taxes and any unit of measure.
- **Fixed prepayment amounts**: set the prepayment as an amount; the percentage is recalculated automatically, and vice versa.
- Automatic Invoicing is now a per-company setting instead of a system parameter.
- Product images on sale orders and their PDFs, numbered order lines (form, PDF, portal) and a redesigned sale order portal page.
- Margin is editable on the order line and recomputes the price; overages on prepaid services can be charged from the invoicing wizard.
- Quotation sections can update the quantity and unit of all their products, hold multi-line descriptions and be saved as templates; quotation templates support the product catalog, subsections and line options.
- Amounts such as margin, delivered % and taxes are stacked inside the order line instead of spread across columns.

## Technical data model changes

- **product.template**: `expense_policy` renamed to `reinvoice_policy`, `visible_expense_policy` to `visible_reinvoice_policy`. `invoice_policy` now shares its selection with the new company-level `sale_invoice_policy`.
- **res.company**: new `sale_invoice_policy` (required, default "Ordered quantities") and `sale_automatic_invoice`, which replaces the `sale.automatic_invoice` system parameter; the automatic-invoice cron switches on only when a company activates it.
- **sale.order**: `require_payment` removed (a prepayment is disabled with `prepayment_percent = 0`); new `prepayment_amount` (Monetary, inverse of `prepayment_percent`), new `analytic_account_id`, and `_prepare_analytic_account_data()` now takes a `plan_id`.
- **sale.order.line**: new `label` field (product name + description) with inverse; `name` is no longer required nor prefixed with the product name (`translated_product_name` removed), so EDI descriptions carry only the real description. New `invoice_policy` (computed and searchable) and `mandatory_product`; `product_uom_id` compute changed (empty for sections, default Units for productless lines); the "product required" SQL constraint is relaxed; `qty_delivered_method` now also depends on `product_id`; new `_is_reinvoicing_line()` and `_is_line_reinvoicable()`.
- **account.analytic.line**: `timesheet_invoice_id` renamed to `reinvoice_move_id`; `so_line` and `order_id` become computed, stored and editable; new synchronisation keeps order lines, analytic accounts and partners aligned; lines already invoiced can no longer be edited or deleted.
- **account.move**: new `service_line_count`, `action_view_services_analytic_lines()` and `_link_analytic_lines_to_invoice()`.
- **Catalog**: line logic moved to the new `product.catalog.line.mixin`; `_get_product_catalog_lines_data()` and `_update_order_line_info()` disappear, and the `catalog_skip_tracking` context key becomes `catalog_update`.
- **Reporting**: `utm.campaign.invoiced_amount` becomes Monetary with a computed currency and loses `company_id`; `crm.team.invoiced`, `invoiced_target` and `update_invoiced_target()` are removed.

## How your habits should change

- Product re-invoicing options are unchanged (No / At cost / Sales price); only the field label changed.
- Set the prepayment to 0 to disable it: the "Online payment" toggle is gone.
- Leaving a line without a product is now allowed by default; enable "Mandatory Product" for the old behaviour.
- The description now contains only the description — Odoo displays the product name next to it.
- Configure automatic invoicing per company, not in system settings.
- To correct an already invoiced service, cancel or adjust the invoice first.

## What you gain by migrating

- Less manual entry for re-invoicing: services and materials captured on the order flow into order lines, delivered quantities and invoices.
- Faster quoting for service and project businesses thanks to description-first lines, section templates and editable margins.
- Cleaner customer-facing documents: real descriptions in EDI and PDFs, product images, numbered lines.
