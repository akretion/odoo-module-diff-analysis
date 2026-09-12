# sale migration guide (15.0 -> 16.0)

## What's new for users

- **Delivery status on sales orders.** At a glance you see whether products were delivered, partially delivered or not, and whether they were delivered on time.
- **Confirmed cancellation.** Cancelling a sales order that was already sent to a customer now requires a confirmation, with an option to email the customer.
- **No payment on canceled documents.** A payment link can no longer be used to pay a canceled sales order or invoice.
- **Pricelist change warning.** Changing a partner's pricelist warns you when open sales orders (including website carts) use it.
- **Clearer down payments.** The way a down-payment invoice is linked to its sales order was reworked for readability.
- **Analytics.** The new analytic distribution widget (plans replacing groups, distribution models) is used on invoices and, in Sales, on order lines.

The coupons/loyalty/eWallet, subscription, Amazon and eCommerce items in the release notes belong to other apps or to Enterprise editions, so they are out of scope for this addon.

## Technical data model changes

**sale.order**
- `tax_totals_json` (Char, JSON string) becomes `tax_totals` (Binary, dict); `_compute_tax_totals_json` is renamed `_compute_tax_totals`.
- `analytic_account_id` is no longer computed: the default coming from analytic default rules is gone, the field must be filled manually.
- UTM fields `campaign_id`, `medium_id`, `source_id` now use `ondelete='set null'` (also on `account.move`): deleting a UTM record no longer deletes orders/invoices.

**sale.order.line**
- Now inherits `analytic.mixin`: new `analytic_distribution` and `analytic_distribution_stored_char`; `analytic_tag_ids` (`account.analytic.tag`) is removed entirely.
- `price_subtotal`, `price_tax`, `price_total`, `price_reduce_taxinc`, `price_reduce_taxexcl` are now precomputed.
- `_get_protected_fields` protects `analytic_distribution_stored_char`; `_prepare_invoice_line` passes `analytic_distribution` (the order's analytic account is injected with 100%).
- `_prepare_analytic_line` becomes `_prepare_analytic_lines` (one list per line, keyed by `move_line_id`, derived from `analytic_distribution`).
- `_convert_to_tax_base_line_dict` defaults `display_type` to `'product'`.

**account.move and settings**
- `partner_shipping_id` moves from `sale` to `account` and is computed instead of set by onchange; the `_get_invoice_delivery_partner_id` and `_onchange_partner_id` overrides are removed; the `fiscal_position_id` compute now lives in account.
- `action_invoice_paid` becomes `_invoice_paid_hook`; `_prepare_invoice` no longer sets `journal_id` or `partner_bank_id`.
- `exclude_from_invoice_tab` is replaced by `display_type = 'product'` in queries.
- `group_sale_delivery_address` is removed from `res.config.settings`.

## How your habits should change

- Distribute analytic costs on order lines with the widget instead of tags; create analytic plans and distribution models, and set the order's Analytic Account manually if you rely on it.
- Expect a confirmation dialog when cancelling an order that was already sent.
- Do not reuse payment links on canceled documents: payment is now refused.
- Use the new delivery status instead of guessing from pickings.
- Third-party or custom modules touching `analytic_tag_ids`, `tax_totals_json`, `exclude_from_invoice_tab` or `action_invoice_paid` need to be adapted before go-live.

## What you gain by migrating

- **Speed:** precomputed line amounts and a fully declarative accounting engine make order confirmation and invoicing much faster (creating a 500-line invoice drops from minutes to seconds).
- **Cleaner analytics:** one distribution widget shared with Accounting, with plans and distribution models replacing ambiguous tags.
- **Safer data:** UTM cleanups no longer cascade into sales orders and invoices.
- **Better customer service:** delivery status, cancellation confirmation, and payments blocked on canceled documents.
