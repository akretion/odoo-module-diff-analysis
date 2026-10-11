# event_product migration guide (19.0 -> 20.0)

`event_product` links event tickets to products: prices, taxes, and what is sold with a registration. This guide covers what changes for you when moving from 19.0 to 20.0.

## What's new for users

The 20.0 release notes mention this addon once, in the Events section:

- **Event combos** - combine event registration and food/beverages in a combo ticket with distinct VAT rates.

That is exactly what `event_product` now delivers: an event ticket can carry a list of **Additional Products**, each taxed with its own taxes. Tickets therefore show a combined price, and the extra products also remain visible as separate lines on sale orders.

Nothing else in the published release notes (eCommerce, Sales, Inventory, Point of Sale, Localizations, ...) concerns event tickets and their products. Features such as the AI product assistant, marketplace connectors, payment terminals or eCommerce donation flows are out of scope here and are not part of this addon.

## Technical data model changes

On the event ticket type (`event.type.ticket`), and therefore also available on event tickets:

**Added**
- `additional_product_ids` - Many2many to `product.product`, label "Additional Products", domain excludes products with `service_tracking = 'event'`. Help text: products included with each registration, also shown separately on sale orders.
- `total_price` - computed price (digits "Product Price"): ticket price plus the sum of the additional products' sales prices.
- `total_price_reduce` - computed with `compute_sudo`: discounted ticket price plus the discounted price of each additional product, using each product's contextual discount.
- The label of `price` changed from "Price" to "Ticket Price" (same field, same computation).

**On event tickets (`event.event.ticket`)**
- Added `total_price_reduce_taxinc` (tax-included total of the reduced price) and `total_price_incl` (tax-included total of the list price). Both are computed with `compute_sudo`, with digits "Product Price".
- Removed `price_reduce_taxinc` and `price_incl`, replaced by the two fields above. Note that `price_incl` could previously be edited; the new `total_price_incl` is computed only.
- Both totals now add, for every additional product, its tax-included price, computed with the taxes matching the event's company.

**Methods**
- `_compute_price_reduce_taxinc` renamed to `_compute_total_price_reduce_taxinc`, `_compute_price_incl` renamed to `_compute_total_price_incl` (which now also depends on `additional_product_ids`).
- New `_compute_total_price` on the ticket type.
- `_compute_price_reduce` now depends on `additional_product_ids`, `product_id`, `price` and `total_price`.

Views, reports, imports/exports or custom code using `price_incl` or `price_reduce_taxinc` must be updated to the new names.

## How your habits should change

- Configure **Additional Products** on the ticket instead of encoding extras in the ticket price; each product then brings its own taxes.
- Read "Ticket Price" as the base and "Total Price / Total Price Reduce" as the bundle price, including the additional products.
- Discounts: the contextual/pricelist discount applies to the ticket and to each additional product, so the tax-included totals already reflect it.
- Additional products are not only a total: they appear as separate lines on sale orders, keeping invoicing traceable.
- A product whose service tracking is an event cannot be selected as an additional product.
- Reports or exports built on the old price fields need adapting.

## What you gain by migrating

- Sell a bundle on a single ticket (registration + food, drinks, goodies) with correct per-product VAT, instead of a flat manually priced ticket under one tax.
- Better accounting accuracy: taxes come from the products themselves.
- Extra products are tracked individually on sale orders, making revenue and invoicing analysis easier.
- A clearer UI with explicit "Ticket Price", "Total Price" and "Total Price Reduce" fields, plus tax-included totals for both list and discounted prices.
- You keep the standard Odoo event and eCommerce experience; only ticket-product pricing is reworked.
