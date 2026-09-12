# account migration guide (16.0 -> 17.0)

## What's new for users

- **Invoice layout overhaul**: a clearer invoice layout, with the possibility to print the total amount in letters (a legal requirement in several countries).
- **Revamped Send & Print wizard**: you now choose which documents (PDF, electronic invoice) and approvals are generated each time you send or download invoices. Nothing is generated automatically at posting anymore.
- **Mass download of documents**: from Send & Print you can grab all documents of a selection; downloading a single invoice returns a zip with every available format (also from the portal).
- **Asynchronous mass sending** shows a banner on the invoices being processed.
- **Tax taxonomy**: tax names now carry a code, a new *Tax Description* field holds the long description, some tax fields can no longer be modified once used, and changes are tracked in the chatter.
- **Matching numbers** are simplified and colour-coded; partial matchings are uniquely identified.
- **Settings**: "Line subtotals tax display" (B2B/B2C) has been merged with the tax rounding method (round per line / globally) in Accounting settings.
- **Localizations**: many new or updated charts of accounts, taxes and reports (Estonia, Greece, Ireland, Kazakhstan, Latvia, Tunisia, …), SODA import with an account-mapping wizard, SAF-T for Lithuania and Denmark.

## Technical data model changes

- **Chart templates are no longer stored in the database**: `account.chart.template`, `account.account.template`, `account.tax.template`, `account.fiscal.position.template` (and related templates) are removed. Chart data now lives in code/CSV inside each localization. `chart_template_id` (Many2one) becomes a `chart_template` Selection. Modules `l10n_generic_coa` and `l10n_multilang` are removed (generic CoA folded into `account`, translations native).
- **Taxes**: `account.tax.repartition.line` now has a single `tax_id` plus a `document_type` (invoice/refund) instead of `invoice_tax_id`/`refund_tax_id`. `account.tax` gains `repartition_line_ids`; `invoice_/refund_repartition_line_ids` become filtered views.
- **Payment terms**: balance/example-amount fields removed, due-date configuration simplified; the early-payment discount is defined on the payment term (single installment only) instead of on the company.
- **Tax groups**: `property_tax_payable/receivable/advance_tax_payment_account_id` become company-scoped fields with a `company_id`.
- **Journals**: `code` is now computed (auto `CSHxx`/`BNKxx` for bank/cash); alias management goes through `mail.alias.mixin.optional` (`alias_name`/`alias_domain` removed).
- **Onboarding fields and methods removed from `res.company`**, replaced by the `onboarding.onboarding.step` framework.
- **Send & Print**: `account.invoice.send` is replaced by `account.move.send` (regular model, asynchronous cron); EDI decoding moves into `account` (`ir.attachment._unwrap_edi_attachments`, `account.move._extend_with_attachments`). New `invoice_pdf_report_id` / `invoice_pdf_report_file` fields keep the generated PDF.
- **Translated fields**: account name, journal name, account group name, tax name and account tag name are now translatable; several computed fields are `precompute`.
- **Method signatures**: `_search_new_account_code(company, digits, prefix, cache=None)`, `get_next_bank_cash_default_code(journal_type, company, cache=None)`, `_create_document_from_attachment(attachment_ids)` (no default). Dashboard helpers are now batched (`_get_*_batched`), old methods kept for compatibility.

## How your habits should change

- A chart of accounts is no longer a set of editable database records: customising it means editing CSV/code, not template forms.
- Invoices are no longer turned into PDFs at posting: use Send & Print to generate the PDF/e-invoice, and delete the PDF to regenerate it.
- Early payment discounts are only available on single-installment payment terms, and are configured on the payment term rather than on the company.
- Bank/cash journal codes are suggested automatically; sales and purchase journals get their email alias through the standard alias mechanism.
- The B2B/B2C subtotal toggle is gone: pick the tax rounding method instead.

## What you gain by migrating

- Chart installation is up to ~4x faster, with no template synchronisation hooks, and localization updates (tax rates, tags) become simple data changes.
- A significantly faster accounting dashboard on large databases, thanks to batched queries and new indexes.
- Full control over generated and sent documents: nothing is produced behind your back.
- A cleaner data model: company-scoped taxes and tax groups, translated names, standard alias and onboarding frameworks — less custom code, easier support and future upgrades.
