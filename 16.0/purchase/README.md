# purchase migration guide (15.0 -> 16.0)

This guide covers the Odoo Community addon `purchase` and what changes for you between 15.0 and 16.0.

## What's new for users

- **Bill price matching** — if the vendor bill price differs from the purchase order price, the cost is corrected, keeping valuation and margins consistent.
- **Purchase history** — a product's purchase history is visible directly on requests for quotation, so buyers can check past prices while negotiating.
- **Receipt status** (with Inventory installed) — purchase orders show at a glance whether products are received, partially received or not received, and whether they arrived on time.
- **New analytic distribution widget** — from the Accounting rework: analytic tags and the single analytic account on a purchase order line are replaced by one widget that splits an amount across several analytic accounts, driven by analytic plans and distribution models.

The "Call for tenders" revamp in the release notes belongs to the RFQ-alternatives addon, not to `purchase`; the remaining Accounting items are out of scope here.

## Technical data model changes

**purchase.order**
- `tax_totals_json` (Char, computed) **removed** → `tax_totals` (Binary, compute `_compute_tax_totals`). The field now carries a dict instead of a JSON string, and the tax summary is computed from the order lines.
- The method `_compute_tax_totals_json` was renamed `_compute_tax_totals`, with the same dependencies (`order_line.taxes_id`, `price_subtotal`, amounts).

**purchase.order.line**
- Now inherits `analytic.mixin`.
- **Removed**: `account_analytic_id` (Many2one `account.analytic.account`) and `analytic_tag_ids` (Many2many `account.analytic.tag`), together with their `account.analytic.default` based computes.
- **Added**: `analytic_distribution` (from the mixin) and `analytic_distribution_stored_char`, computed by `_compute_analytic_distribution_stored_char` (depends on `product_id`, `order_id.partner_id`) through `account.analytic.distribution.model`.

**Vendor bill creation**
- `_prepare_account_move_line` no longer sets `sequence`, `move_id`, `currency_id`, `date_maturity` or `partner_id`; `display_type` now defaults to `product`. Analytic values are passed as `analytic_distribution`.
- The order→bill flow no longer builds lines through the Form emulator and onchange chain; lines are added directly and dynamic lines (taxes, payment terms) are generated on save.
- The explicit "Please define an accounting purchase journal" error was removed from the bill preparation helper; the journal is now resolved through the accounting defaults.

No other method signatures changed in this addon.

## How your habits should change

- **Analytics**: forget analytic tags. Use the new distribution widget on purchase order lines (and on vendor bills), and set up analytic plans plus distribution models to prefill it. The distribution is carried over to the generated bill.
- **Bills generated from orders**: values are synchronized on save, and the form saves when you switch tabs. The tax summary and amounts therefore refresh at save time rather than instantly as you type.
- Some onchange-driven behaviours on bill lines were intentionally dropped — for example, changing a line's balance or amount in currency no longer recalculates the unit price. Review any manual tweak you used to do there.
- Make sure each company has a purchase journal configured: the missing-journal error now surfaces later in the flow.

## What you gain by migrating

- **A major performance win on large documents**: creating a bill with 500 lines drops from roughly 2 minutes to about 4 seconds with the new engine, and invoices with many lines are up to ~30 times faster.
- **Cleaner analytics**: one distribution widget instead of tags plus a single account, with reusable plans and distribution models.
- **Better purchasing visibility**: receipt status on orders, purchase history on RFQs, and price matching between order and bill.
- **A modern accounting core** (computed fields, structured `tax_totals`) that is the foundation for EDI/OCR flows and for all later versions. Staying on 15.0 means keeping the old synchronous onchange engine that 16.0 replaced.
