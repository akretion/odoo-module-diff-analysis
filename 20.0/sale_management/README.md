# sale_management migration guide (19.0 -> 20.0)

This guide summarises what changes for Quotation Templates / Sales Management users moving from Odoo 19.0 to 20.0.

## What's new for users

- **Description-only sales order lines.** Order lines no longer require a product: type a description, then set quantity, unit of measure, price, discount and taxes on the line. A new **Mandatory Product** setting in the Sales settings restores the 19.0 product-first behaviour for companies that need it.
- **The description field becomes the main field.** It behaves as a combined product + description textarea and lets you search for a product directly from the description. When the product is readonly, editing the description no longer opens the product autocomplete.
- **Pick a template when creating an order.** Clicking "New" now lets you select a quotation template, and also edit or delete templates from that same menu. The field is renamed "Template" and moved to the Other Info tab. The old per-company default template is gone.
- **Prepayment.** The "Online Payment" flag on templates was removed: a template now carries only a prepayment percentage (0% = no prepayment). On the order, a prepayment can be set as a **fixed amount**, with the percentage recalculated automatically.
- **Section and optional templates** keep taxes, unit price and discount for productless lines, including currency conversion when the template is applied to an order in another currency. Multi-line section descriptions are supported.
- Productless lines are considered by loyalty programs, and by commissions/achievement rules without product restrictions; they show in sales reports under their own name.
- Combo lines display only the combo name ("X quantity" suffix removed); Edit Configuration now appears on hover and the stacked description is always visible.

## Technical data model changes

- `sale.order.template`: new `currency_id` and computed `has_productless_lines`.
- `sale.order.template.line`: new technical `discount`, `price_unit` and `tax_ids` (used to carry values for productless lines), plus computed `mandatory_product`, read from the `sale.mandatory_product` configuration parameter. The accountability constraint no longer requires a product, only a unit of measure. Units of measure now default to Units and follow the product once one is selected.
- Method signature changes: `_prepare_order_line_values(fiscal_position, currency)` and `prepare_section_template_order_lines(order_changes, fiscal_position_id, company_id, currency_id, fields_spec)`. Section templates now also store the order currency.
- Removed: the `_compute_name` / `_use_template_name` overrides (a template description no longer replaces the line name), and the `res.company.sale_order_template_id` field (model file deleted) together with `res.config.settings.company_so_template_id`. `sale.order.sale_order_template_id` is no longer computed from a company default, is no longer precomputed, and its label is now "Template".
- `sale.order.template.require_payment` was removed; `prepayment_percent` (label "Prepayment") must be between 0 and 1, where 0 means no prepayment. `_onchange_prepayment_percent` and the `require_payment` compute overrides were dropped.
- Invoicing policy: the `default_invoice_policy` configuration parameter becomes the company-dependent `sale_invoice_policy`. A new searchable computed `invoice_policy` on sale order lines returns the product's policy, or falls back to the company setting for productless lines.

## How your habits should change

- Instead of maintaining a default template per company, choose the template at creation time. If your process depends on a product on every line, enable Mandatory Product.
- On productless lines you enter price, discount and taxes yourself; taxes default to the company's default sales taxes.
- To disable prepayment, set the percentage to 0 instead of unchecking "Online Payment".
- UoM behaviour: in mandatory-product mode the selection stays restricted to the product's compatible UoMs; without a product, any UoM can be chosen.
- Description search is disabled while Mandatory Product is active: use the Product field in that mode.
- Removing a product from an existing line now clears the description, and re-adding a product already brought in by a template no longer reuses the template description.

## What you gain by migrating

- Faster quoting for services, small businesses and sub-contracted work: description-only lines remove the need to create a product for every line, while taxes, invoicing and reporting keep working.
- Less data maintenance and a lighter product catalog, with a setting to enforce products where discipline is required.
