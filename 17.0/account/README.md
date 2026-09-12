# account migration guide (16.0 -> 17.0)

This guide covers what changes for the `account` addon between Odoo 16.0 and 17.0, for functional users and for the migration project.

## What's new for users

- **Early payment discount & payment terms.** The display of installments and early-payment discount dates was reworked, and the early discount is now configured on the payment term itself.
- **Tax display on invoices.** "Line subtotals tax display" and the rounding method were merged into Accounting settings: with *round per line* a tax-excluded column is always shown and a tax-included column is optional; with *round globally* only tax-excluded columns are available.
- **Simpler invoice actions.** The Debit Note button was moved into the invoice action menu.
- **Matching numbers.** They are colour-coded in Journal Items, partial matchings get a unique identifier, and a matching number can now be imported on lines.
- **Reconciliation.** The manual reconciliation widget was removed: lines are silently reconciled unless a write-off is required, which opens a new wizard.
- **Invoices.** Delivery date is now a standard field on invoices; the revamped "send and print" wizard lets you choose which documents to generate, and portal/ëmail downloads return all formats including electronic invoices.
- **Payment scam protection.** A vendor bank account must be marked as trusted before it can be used for an outgoing payment.
- **Bank statements.** The bank statement PDF report layout was cleaned up.

## Technical data model changes

**Tax distribution lines merged** (`account.tax.repartition.line`): `invoice_tax_id` and `refund_tax_id` are removed and replaced by a single `tax_id` (cascade delete, company check); `document_type` (Invoice/Refund) becomes a stored, required field instead of a computed one; `company_id` is now related to the tax company. `account.tax` gains `repartition_line_ids` (copyable), while `invoice_repartition_line_ids` / `refund_repartition_line_ids` become filtered views on the same lines. The "both taxes set" constraint and the compute methods are gone.

**Payment terms** (`account.payment.term`): new `early_discount`, `discount_percentage` (default 2), `discount_days` (default 10), `last_day_for_discount`, `currency_id`; `early_pay_discount_computation` moves from `res.company` (and settings) to the payment term; `example_amount` becomes a read-only Monetary field; "Display terms on invoice" becomes "Show installment dates" (on by default). On `account.payment.term.line`, `months`, `days`, `end_month`, `days_after`, `discount_percentage` and `discount_days` are removed in favour of `delay_type` (days after invoice date / end of month / end of next month), `nb_days` and a computed `value_amount`; the "Balance" value option disappears (percent or fixed only), percent lines must total 100 %, and early discount is only allowed on a single 100 % line. `discount_percentage` is removed from `account.move.line`, and the early-payment eligibility helper moves from the line to `account.move`.

**Tax display groups**: the `group_show_line_subtotals_tax_excluded/included` groups, the `show_line_subtotals_tax_selection` setting and the related `res.users` constraint are removed; `account.move` and `account.move.line` gain a read-only related `tax_calculation_rounding_method`.

**Onboarding**: eleven onboarding-state fields and their methods are removed from `res.company`; progress is now stored on the generic `onboarding.onboarding` / `onboarding.onboarding.step` models.

**Journal email aliases**: `account.journal` now inherits `mail.alias.mixin.optional`; `alias_name` becomes a normal writable field instead of a computed field with a type-based inverse.

## How your habits should change

- On a tax form, invoice and refund lines now live in one distribution list with a "Related to" column; the two tabs are just filtered views of it.
- Configure early payment discounts on the payment term (single 100 % line), not per line, and check the cash-discount tax reduction there instead of in company settings.
- Payment-term lines are expressed as a delay type plus a number of days; percentages must add up to 100 %.
- The B2B/B2C tax-display setting is gone: the display follows the rounding method.
- Journal aliases are typed directly on the journal.
- Reconciliation and matching are more automatic; use the wizard only when a write-off is needed.

## What you gain by migrating

- A cleaner tax model (one distribution list per tax) that is easier to maintain and to audit.
- Payment terms anyone can understand, with early payment discount and its tax treatment available in Community and properly previewed.
- One consistent tax-display policy, driven by the rounding method, instead of conflicting user groups.
