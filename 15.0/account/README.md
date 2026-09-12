# account migration guide (14.0 -> 15.0)

## What's new for users

- **Foreign VAT fiscal positions.** A fiscal position can now carry a *Foreign Tax ID* and the country (and states) it belongs to. This lets you apply the VAT of another country than your own — for instance a French VAT on top of your Belgian VAT — and declare it there. Invoices print the foreign VAT number instead of the company one when such a fiscal position is applied.
- **Taxes are now tied to a country.** On an invoice, only the taxes of the relevant country are offered: your company's fiscal country, or the foreign country defined by the applied fiscal position.
- **Payment methods on bank/cash journals have been redesigned.** You can configure several manual payment methods on the same journal (e.g. to distinguish card owners), each with its own name, sequence and optional outstanding account.
- **Several payment acquirers can now post into the same journal.**

## Technical data model changes

Taxes & accounting:
- `res.company`: `account_tax_fiscal_country_id` renamed to `account_fiscal_country_id`; new computed `account_enabled_tax_country_ids`.
- `account.tax`: new required `country_id` (taxes are no longer just related to the company's country); `country_code` now relates to it. Tax repartition lines lose `tax_fiscal_country_id`, and `use_in_tax_closing` now defaults to True.
- `account.move`: new computed `tax_country_id` and a new constraint `_validate_taxes_country` refusing taxes outside the allowed country. `account.move.line.tax_fiscal_country_id` was removed.
- `account.fiscal.position`: new `foreign_vat` and `company_country_id`, plus constraint `_validate_foreign_vat_country`.
- `account.chart.template`: new `country_id`, used to set each tax's country and the company's fiscal country.
- Cash-basis entries now carry the fiscal position.
- Method renamed: `account.move._unlink_except_parts_of_chain` -> `_unlink_forbid_parts_of_chain`.

Payments & journals:
- New model `account.payment.method.line` (name, sequence, payment_method_id, payment_account_id, journal_id).
- `account.journal`: `inbound_payment_method_ids` / `outbound_payment_method_ids` (many2many) replaced by `inbound_payment_method_line_ids` / `outbound_payment_method_line_ids` (one2many). `payment_debit_account_id` / `payment_credit_account_id` and `at_least_one_inbound/outbound` were removed. New `available_payment_method_ids`, `selected_payment_method_codes`, `_get_available_payment_method_lines()`.
- `account.payment`: `payment_method_id` replaced by `payment_method_line_id`; new stored `outstanding_account_id`; `available_payment_method_ids` becomes `available_payment_method_line_ids`.
- `account.payment.method`: `sequence` removed, new unique constraint on code + payment type; auto-created methods only apply to eligible journals.
- Setting a payment account on a method line now forces that account to be reconciliable.

## How your habits should change

- Every tax must be assigned a country. If you used a tax for several countries, split it and use fiscal positions instead.
- A foreign VAT fiscal position cannot be created inside your own fiscal country with your company's own VAT number, and must be assigned states when your country has some.
- Outstanding Receipts/Payments accounts are no longer set per journal: define them on the company or, better, per payment method line on the journal.
- Payment methods are now managed as lines on the journal; only the *manual* method can be repeated, and two lines of the same type cannot share the same name on one journal.
- Removing a method line from a journal no longer deletes it if payments used it — the link is simply broken.
- Tax repartition tags must belong to the tax's country.

## What you gain by migrating

- Correct handling of **foreign VAT**: charge it, mention it on invoices, and keep tax reporting consistent.
- Fewer configuration mistakes: taxes and tags from other countries are hidden and blocked by constraints.
- A cleaner, more flexible payment setup: outstanding accounts per payment method, several manual methods per journal, and multiple payment acquirers on one journal.
