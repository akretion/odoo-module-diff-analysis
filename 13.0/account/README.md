# account migration guide (12.0 -> 13.0)

## What's new for users

- Customer invoice communication is now configured **per journal**: Free, Based on Customer or Based on Invoice, in the Odoo or European (SEPA RF creditor reference) standard. It is no longer a single company-wide setting.
- Payment registration is simpler: one payment per invoice selected, with the amount locked to the invoice residual.
- Reconciliation write-off models accept **several taxes per line** instead of a single one.
- Journals: a default journal is proposed to prevent errors; the "post at bank reconciliation" checkbox becomes a clear **Post At** choice (Payment Validation / Bank Reconciliation).
- Chatter on journals and journal entries; moves "to check" on the accounting dashboard; invoice/bill and payment views reorganised and relabelled.
- Tax lock dates for closed tax periods; posted entries can be locked per journal (hash).
- Bank reconciliation: easier matching with suspense entries; manual reconciliation for a partner always shows all open entries.
- Belgium: vendor bills can be imported from UBL 2.1 e-invoices (l10n_be_edi).
- Customer invoices, vendor bills and journal entries now share the same underlying document model (account.move).

## Technical data model changes

Reconciliation models (`account.reconcile.model` and `...model.template`)

- `tax_id` -> `tax_ids` (many2many), `second_tax_id` -> `second_tax_ids`.
- `force_tax_included` / `force_second_tax_included` are only usable with exactly one tax; new non-stored `show_force_tax_included` / `show_second_force_tax_included` helpers drive the form.

Payments

- `account.abstract.payment` and the `account.register.payments` wizard are removed; a new `account.payment.register` wizard replaces them.
- `account.payment`: `multi` and `group_invoices` gone; state `posted` relabelled "Validated"; new `reconciled_invoices_count`.

Journals / company

- `account.journal`: new `invoice_reference_type` and `invoice_reference_model`; `post_at_bank_rec` (boolean) replaced by `post_at` (selection).
- `res.company.invoice_reference_type` removed (moved to journals).

Account moves

- `account.move`: `auto_reverse` and `reverse_date` removed, `auto_post` added; `amount` now has an inverse; `currency_id` is computed together with `amount`; a cron posts draft auto-post entries.
- `account.move.line`: `debit_cash_basis`, `credit_cash_basis`, `balance_cash_basis` removed.
- Renames: `invoice_vendor_display_name` -> `invoice_partner_display_name`, `invoice_vendor_icon` -> `invoice_partner_icon`, `invoice_has_matching_supsense_amount` -> `invoice_has_matching_suspense_amount`.

Misc

- `account.fiscal.position.zip_from` / `zip_to`: Integer -> Char (alphanumeric postal codes).
- `account.invoice.import.wizard` removed from `account`.

## How your habits should change

- Set the invoice communication type and standard on each sales journal, not in the company settings.
- Registering a payment for several invoices now creates one payment per invoice; you can no longer group them or change the amount.
- In write-off models, select all applicable taxes; "tax included in price" only appears when a single tax is chosen.
- Payments are "Validated" instead of "Posted"; entries are "Draft" instead of "Unposted".
- Fiscal position ZIP ranges are text: they are compared character by character and may contain letters (e.g. Canadian postal codes).
- Cash-basis columns of journal items no longer exist; use tax cash basis entries and reports instead.

## What you gain by migrating

- Fewer configuration mistakes and a cleaner, faster accounting interface (views, labels, dashboard).
- Faster, less error-prone payment registration and reconciliation.
- European-ready invoice references (SEPA RF), multi-tax write-offs, and flexible fiscal positions for countries with alphanumeric postal codes.
- One single document model for invoices, bills and entries, which simplifies customisations, data imports and integrations.
- A supported version: 12.0 no longer receives fixes, while 13.0 keeps your accounting compliant.
