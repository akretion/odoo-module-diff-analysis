# account migration guide (14.0 -> 15.0)

## What's new for users

- **Chart of accounts:** the account form view has been revamped and now logs changes in a chatter, so you can see who touched an account and why.
- **Journals & payment methods:** payment method management on bank/cash journals has been improved. Methods are now editable per-journal lines: you can keep several *manual* methods (for example one per card owner) and give each of them its own outstanding receipts/payments account.
- **Reconciliation:** reconciliation models got a readability pass, and the "amount matching" idea becomes a **payment tolerance**, expressed either as a percentage or as a fixed amount, with a counterpart account for the difference.
- **Taxes:** the tax totals block on invoices breaks down amounts per tax group, which makes discounts and withholding taxes easier to read.
- **VAT:** a fiscal position can now carry a *Foreign Tax ID*, letting you invoice under a foreign VAT number instead of your company one. Producing the foreign VAT return itself still relies on the Enterprise tax report engine.

## Technical data model changes

**Payment methods**
- New model `account.payment.method.line` (name, payment method, journal, payment account).
- `account.journal`: removed `payment_debit_account_id`, `payment_credit_account_id`, `inbound/outbound_payment_method_ids` and `at_least_one_inbound/outbound`; added `inbound/outbound_payment_method_line_ids`, `available_payment_method_ids` and `selected_payment_method_codes`.
- `account.payment`: `payment_method_id` becomes `payment_method_line_id` (the old name stays as a stored related field), `available_payment_method_ids` -> `available_payment_method_line_ids`, `hide_payment_method` -> `hide_payment_method_line`, plus a new `outstanding_account_id`. Only the `manual` code can be repeated on one journal, and a method line already used by a payment cannot be deleted.

**Cash basis**
- `account.move.line.tax_exigible` is removed; exigibility is now computed on the fly (`_get_tax_exigible_domain`).
- `account.move`: `tax_cash_basis_move_id` becomes `tax_cash_basis_origin_move_id`; added `tax_cash_basis_created_move_ids` and the stored `always_tax_exigible`.
- Cash-basis and non-cash-basis taxes can now be mixed on the same line, unless they share the same tax tag (new constraint).

**Taxes & countries**
- `res.company.account_tax_fiscal_country_id` is renamed `account_fiscal_country_id`; a new `account_enabled_tax_country_ids` lists the countries used for tax purposes.
- `account.tax.country_id` is now required; `tax_fiscal_country_id` disappears from taxes, repartition lines and move lines; `account.move.tax_country_id` is added and enforced by a constraint.

**Reconciliation**
- `account.reconcile.model` now inherits `mail.thread` (chatter and change tracking).
- `match_total_amount` -> `allow_payment_tolerance`, `match_total_amount_param` -> `payment_tolerance_param`, new `payment_tolerance_type`; new line amount type "Percentage of statement line", and the statement line `reconcile` method accepts an `allow_partial` argument.

## How your habits should change

- Review every reconciliation model: several options were renamed, the tolerance has a type, and the old automatic bypass when the label exactly matched the payment reference no longer exists.
- Set outstanding accounts on each payment method line rather than once per journal; the company-level accounts remain as fallback.
- Give your taxes a country. Taxes coming from another country than the invoice's fiscal country are refused.
- Cash-basis entries can no longer be reset to draft: open them from the new "Cash Basis Entries" smart button on the source invoice.

## What you gain by migrating

- Payment methods that match your real bank/cash workflow, with dedicated outstanding accounts.
- Predictable reconciliation rules, with a tolerance you control.
- Withholding taxes: cash-basis and regular taxes can coexist on a single invoice line.
- Stronger traceability: chatter on accounts and reconciliation models, cash-basis entries one click away.
- Foreign VAT invoicing and stricter tax-country controls for safer tax reporting.
