# sale migration guide (15.0 -> 16.0)

This guide covers the `sale` addon (Community edition) and summarises what functional users notice when moving from 15.0 to 16.0.

## What's new for users

- **Canceled order confirmation**: cancelling a confirmed/sent sales order now asks for confirmation, with the option to email the customer.
- **Canceled order/invoice payment blocked**: a canceled order can no longer be paid through a payment link.
- **Delivery status**: sales orders show at a glance whether products were delivered, partially delivered or not delivered, and whether they were delivered on time.
- **Down payments readability**: the link between a down payment invoice and its sales order is clearer.
- **Partner pricelists**: changing a partner's pricelist warns you when open sales orders (including the website cart) still use it.
- **Loyalty, coupons and eWallets**: promotions and loyalty programs are now shared between Sales, eCommerce and Point of Sale.
- **Analytics**: a new analytic distribution widget replaces analytic tags, with analytic plans replacing groups and distribution models prefilling values.

## Technical data model changes

- `pre_compute=True` was renamed `precompute=True` on ~30 sale.order / sale.order.line fields: salesperson, customer, invoice/delivery address, pricelist, currency, payment terms, fiscal position, sales team, terms; line description, unit price, discount, quantity, UoM, packaging, delivered/to-invoice quantities and amounts.
- sale.order.line: `analytic_tag_ids` removed; the model now inherits `analytic.mixin` and stores `analytic_distribution`; new computed `analytic_distribution_stored_char` (a protected field); `price_reduce_taxinc` / `price_reduce_taxexcl` are now precomputed.
- sale.order: `tax_totals_json` (Char, JSON string) replaced by `tax_totals` (Binary, dict); `analytic_account_id` is no longer computed from analytic defaults, it is a plain field.
- account.move (sale extension): `partner_shipping_id` field removed (now handled by account, computed); removed `_onchange_partner_id`, `_get_invoice_delivery_partner_id` and the sale-level fiscal position compute; `action_invoice_paid` renamed to `_invoice_paid_hook`.
- account.move.line: `analytic_tag_ids` removed; `_prepare_analytic_line` becomes `_prepare_analytic_lines`, values keyed by `move_line_id`.
- UTM fields (`campaign_id`, `medium_id`, `source_id`) on sale.order and account.move now use `ondelete='set null'`.
- Sales settings: the `group_sale_delivery_address` ("Customer Addresses") checkbox was removed.
- Invoices created from an order no longer force `journal_id` / `partner_bank_id`; accounting computes them.

## How your habits should change

- Configure **analytic plans** and **distribution models** instead of analytic tags and analytic defaults, then distribute amounts directly on the SO line widget.
- The SO header analytic account is not auto-filled from the partner anymore: set it yourself or let distribution models fill it.
- The "Customer Addresses" toggle in Sales settings is gone; the delivery address is now managed on the invoice side.
- Cancelling a confirmed order asks for confirmation; emailing the customer is optional.
- After a pricelist change on a partner, expect a warning about open quotations/carts and refresh prices with the pricelist update button.

## What you gain by migrating

- Much faster invoicing from sales orders: the accounting engine refactor took a 500-line invoice from about 2 minutes down to about 4 seconds, and orders also generate invoices through that engine.
- Cleaner, auditable analytic analysis: multi-account distribution, plans and models, with reports breakable down by account and plan.
- More reliable sale fields: orders and lines created in batch (imports, integrations, EDI) get correct computed values immediately.
- Fewer hidden side effects: deleting a UTM record no longer cascades, canceled documents cannot be paid, and delivery/pricelist alerts surface issues early.
- A supported, up-to-date base: 16.0 keeps your customisations on a data model aligned with the new accounting engine.
