# account_edi_ubl_cii migration guide (17.0 -> 18.0)

This Community addon generates and imports e-invoice files (UBL/BIS Billing 3.0, Factur-X/CII, XRechnung, NLCIUS, A-NZ, SG) and drives the invoice "Send & Print" flow. In 18.0 it was refactored around per-contact sending preferences.

## What's new for users

- **Preferred invoicing method per contact.** Each customer now has a preferred sending method and e-invoice format. The Send & Print wizard pre-selects it (email by default), so batch sending no longer means ticking boxes for every run.
- **Send & Print visibility** was improved, making it clearer which documents will be generated and sent.
- **Mandatory declarations follow the sending.** Legally required B2G/e-invoice declarations are applied by default when you send, instead of relying on a manually ticked box.
- **New pre-flight alerts.** Before generating a UBL file, Odoo warns you when your company or a partner is missing a Peppol EAS / Peppol Endpoint, or when an invoice has no recipient bank account. The generated file would otherwise be incomplete.
- **Better UBL import.** When you import a UBL (XML) invoice, Odoo fills the bank account found in the XML on the partner when possible.
- **Enrich imported lines.** You can add information (products, analytic distribution, ...) to lines created from an imported e-invoice without altering imported amounts or taxes.

The release notes list many country-specific e-invoicing additions; those belong to the localisation modules, not to this one.

## Technical data model changes

Added:
- `account.move.send` is now extended by this addon to generate the XML, embed the PDF in the XML and embed Factur-X inside the PDF.

Removed fields:
- `res.company.invoice_is_ubl_cii` (the `res.company` model was removed from the addon).
- `res.config.settings.invoice_is_ubl_cii`.
- On `account.move.send`: `enable_ubl_cii_xml`, `checkbox_ubl_cii_label`, `checkbox_ubl_cii_xml`.

Renamed / new fields:
- `res.partner.ubl_cii_format` **renamed** to `res.partner.invoice_edi_format`. The field now lives in `account` and this addon extends its selection (`selection_add`). Stored values keep the same meaning, but custom code, filters, import templates or reports referring to `ubl_cii_format` must be updated.
- New computed `res.partner.is_ubl_format` (True when the contact's format is one of UBL/CII).
- `res.partner.invoice_edi_format` is no longer computed by this addon: the country-based suggestion moved to `_get_suggested_invoice_edi_format()`.

Method signature changes (relevant for customisations):
- `res.partner._get_edi_builder()` -> `_get_edi_builder(invoice_edi_format)`, now `@api.model` (it no longer reads the partner).
- `res.partner._get_ubl_cii_formats()` now returns a **list** (`['ubl_bis3', 'xrechnung', ...]`); the old mapping became `_get_ubl_cii_formats_by_country()`.
- `account.move._need_ubl_cii_xml()` -> `_need_ubl_cii_xml(ubl_cii_format)`; it no longer tests the PDF presence and relies on the requested format rather than the partner default.
- `_get_placeholder_mail_attachments_data(move, extra_edis=None)`, `_get_invoice_extra_attachments`, `_link_invoice_documents(invoices_data)` are now batch-oriented.

## How your habits should change

- Forget the company-level "Generate Peppol format by default" setting: it no longer exists. Set the e-invoice format per contact instead.
- Set each contact's preferred sending method (Post / Email / Peppol / download) once; batch sends will respect it.
- In single mode you can still push an invoice through several channels, but only the preferred one is pre-checked. Using one channel per partner avoids duplicate invoices for your customers' accountants.
- Fill in Peppol EAS and Peppol Endpoint on your company and on your Peppol partners, and set a recipient bank on invoices, to avoid incomplete files.
- If you have custom code touching `ubl_cii_format` or `_get_edi_builder()`, plan an update.

## What you gain by migrating

- Send e-invoices in batch reliably, with per-partner defaults instead of per-run checkboxes.
- Fewer errors: format, EAS/Endpoint and bank details are checked before the file is produced.
- Compliant, portable output: the PDF is embedded in the UBL XML and a Factur-X XML is embedded in the PDF, so recipients get a single file, and Factur-X files are PDF/A-3.
- Cleaner legacy: one `invoice_edi_format` shared with the rest of Accounting, and no duplicated company setting.
- Safer imports: UBL bills fill the partner bank; imported lines can be enriched without altering amounts.
