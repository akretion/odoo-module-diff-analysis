# sale_management migration guide (13.0 -> 14.0)

Scope: Odoo Community (OCA-style deployment). Covers the `sale_management` addon (Quotations, Sales Orders, quotation templates). Release-notes entries that only concern Enterprise-only apps are deliberately left out.

## What's new for users

- **Quotation templates are simplified.** Price and discount are no longer stored on template lines or on optional products. A template now describes *what* you sell (product, quantity, unit of measure); *how much* it costs comes from the product price and the pricelist.
- **Pricelists.** Prices are recomputed when you change the pricelist on a sales order, and a pricelist report can be generated and printed from a product.
- **Orders.** Categorize orders with tags. Confirmed orders with delivered items now show a wizard guiding users on how to handle them when they try to cancel.
- **Invoicing.** Notes from the sale order are copied to the invoice, and the default invoice policy is "based on delivered quantities".
- **Margin visibility.** Margins can be displayed both as a percentage and as an amount on quotations and sales orders (Sales margin app).
- **Taxes & Incoterms.** You can define whether country, state and zip are required to compute taxes; Incoterms were updated to the 2020 legislation.

Beyond this, the release notes say nothing specific to this addon: the rest comes from the code, below.

## Technical data model changes

Removed fields (no field, model or public method signature was added):
- `sale.order.template.line`: `price_unit` (Float, required, digits Product Price) and `discount` (Float, digits Discount).
- `sale.order.template.option`: `price_unit` and `discount`.

Changed behaviour:
- `onchange_sale_order_template_id`: generated lines now take `price = product_id.lst_price` (public price). If a pricelist is set, its price is used; when the pricelist discount policy is *without_discount*, the gap between public and pricelist price is shown as a discount (`max(0, (public - pricelist) * 100 / public)`), otherwise the pricelist price replaces the public price. The old code started from the template's `price_unit` and stacked discounts as `100 - ((100 - discount) * (100 - line.discount) / 100)`.
- `_compute_option_data_for_template_change` was rewritten on the same pattern for optional products.
- `_onchange_product_id` no longer sets a price; `_onchange_product_uom` was removed from both template models.
- `sale.order.template.line.create` no longer forces `price_unit = 0` on section/note lines, and the `non_accountable_fields_null` SQL constraint no longer checks `price_unit`.

Data: stored prices disappear from existing templates. A companion upgrade script (odoo/upgrade#972) ships with this change; plan a review of your templates after migrating.

## How your habits should change

- Stop typing a unit price into quotation templates. Put the selling price on the product, or in a pricelist for tiered or customer-specific pricing.
- A template line is now product + quantity + UoM + description only.
- Changing the unit of measure on a template line no longer recalculates any price.
- The quotation is priced when the template is applied, not when the template is written.
- If you used a template discount as a permanent rebate, move it into a pricelist or a discount policy.
- Optional products follow the same logic: their price also comes from the product or pricelist.

## What you gain by migrating

- One single source of truth for prices: pricelists. No more template price conflicting with the pricelist or the product price.
- Cleaner discount logic: discounts are computed against the public price and never go negative, instead of stacking template and pricelist discounts.
- Consistent pricing rules for both template lines and optional products.
- Plus the wider 14.0 Sales improvements: order tags, guided cancellation, notes carried to invoices, invoice policy based on delivered quantities, pricelist recomputation and margin display.
