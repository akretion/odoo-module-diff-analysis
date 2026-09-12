# account_edi_ubl_cii migration guide (16.0 -> 17.0)

## What's new for users

- **Send & Print wizard rebuilt**: documents are no longer generated automatically at posting. The wizard lets you choose which documents to generate (PDF, UBL/CII e-invoice) and which approvals to request. The e-invoicing format is no longer set on the journal — it is **configured on the contact**.
- **EDI format and Peppol fields on the contacts list view** show at a glance which format a partner uses.
- **UBL import now handles payment terms**: cash discounts and fixed taxes from the file are taken into account.
- **Factur-X files detect the invoice or credit note type from their content**; invoice upload is harmonized between Accounting and Documents.
- **Mass download**: the download option in "Send & Print" grabs all documents for a selection of invoices; a single invoice returns a ZIP with the PDF and the electronic invoice.
- **Vendor bill import** from UBL 3.0 files matches purchase order lines and autocompletes the bill from unit price and product name.
- **PEPPOL e-invoicing for Australia / New Zealand** (A-NZ BIS Billing 3.0).

Not included: Peppol network onboarding is an Enterprise service, not part of this Community module.

## Technical data model changes

Models no longer overridden here: `mail.template`, `account.edi.document`, `account.edi.format`, `ir.actions.report`. Journal-level EDI configuration fields are gone.

`res.partner` (stored, computed, editable):
- `ubl_cii_format`: facturx (CII), xrechnung (UBL), nlcius, ubl_bis3, ubl_a_nz, ubl_sg.
- `peppol_endpoint`: Peppol Endpoint ID.
- `peppol_eas`: Peppol e-address scheme (EAS) code.

`account.move`:
- `ubl_cii_xml_file` (Binary attachment) + `ubl_cii_xml_id` (computed Many2one), replacing separate UBL/CII fields.

Wizard: `account.move.send` replaces `account.invoice.send`; this addon adds `enable_ubl_cii_xml`, `checkbox_ubl_cii_xml` and `checkbox_ubl_cii_label`.

Method signature changes (custom code):
- `_import_invoice(...)` becomes `_import_invoice_ubl_cii(invoice, file_data, new=False)`
- `_get_import_document_amount_sign(tree)`: `filename` removed
- `_import_fill_invoice_form(invoice, tree, qty_factor)`: `journal` removed
- `_import_fill_invoice_line_taxes(tax_nodes, invoice_line, inv_line_vals, logs)`
- `_import_retrieve_partner(tree, invoice)`
- `_retrieve_partner` / `_retrieve_product` moved to `res.partner` / `product.product`
- `_find_value` replaced by the `find_xml_value` tool
- Builder inference moved to `account.move._get_ubl_cii_builder_from_xml_tree`; import entry point is `account.move._get_edi_decoder`

## How your habits should change

- **Configure e-invoicing per contact**, not per journal: set the format, Peppol endpoint and EAS on the customer/vendor. Country defaults still apply (DE -> XRechnung, FR -> Factur-X, NL -> NLCIUS, AU/NZ -> A-NZ, SG -> SG BIS, other EAS countries -> Peppol BIS 3.0).
- **Journal checkboxes for UBL/CII no longer exist**; the format lives on the partner.
- **Generate e-invoices from Send & Print**, not by posting. To regenerate a document, delete the existing PDF/XML attachment and generate again.
- **Replace old country-specific partner fields** (`l10n_nl_oin`/`l10n_nl_kvk`, `l10n_lu_peppol_id`, `l10n_sg_unique_entity_number`, `l10n_no_bronnoysund_number`) with `peppol_endpoint` + `peppol_eas`.
- Generated PDFs always embed a Factur-X XML, and generated UBL XML files carry the PDF inside them.

## What you gain by migrating

- One clear place to configure e-invoicing: the contact, with explicit format and Peppol identifiers.
- Full control over what leaves Odoo: the Send & Print wizard previews documents, and mass sending no longer blocks on one invoice.
- Better incoming documents: improved partner, tax and product matching, move-type detection on Factur-X, purchase-order matching on UBL vendor bills.
- Reliable inter-operability: PDFs ship with embedded Factur-X, UBL XMLs with the PDF, and portal/ZIP downloads deliver every format at once.
- Alignment with Odoo 17's new EDI architecture (no more `account.edi.format`), easing future upgrades.
