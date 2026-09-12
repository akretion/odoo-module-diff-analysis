# account_edi_ubl_cii migration guide (17.0 -> 18.0)

This addon generates and imports electronic invoices (UBL/BIS Billing 3.0, Factur-X/CII, XRechnung, NLCIUS, A-NZ, SG) in the Community edition. Odoo 18.0 brings a deep refactor of the Send & Print flow and of the XML import/export helpers.

## What's new for users

- **Preferred invoicing settings on contacts.** You now define the e-invoice format (and sending method) per contact in its Accounting tab, so batch sending applies the right format automatically. The old company-wide "Generate Peppol format by default" checkbox is gone.
- **A simpler Send & Print.** Fewer ambiguous checkboxes; the format and method are preselected from the partner. Odoo warns you before generating an incomplete file (missing Peppol Endpoint/EAS on your company or the customer, missing recipient bank account).
- **Download the UBL file directly** from the invoice (new action), without going through Send & Print.
- **Better import of received UBL files:** the bank account found in the XML is filled on the partner, partners can be matched through their Peppol Endpoint (EAS), files whose namespaces are declared line by line are now read correctly, and allowances/charges are imported as proper lines.
- Improved support for country-specific flavours (e.g. Colombia, Singapore tax-exempt export, customisable Note).

Note: sending invoices *on* the Peppol network relies on the separate Peppol connector (Enterprise). This Community addon produces and reads the files; it does not itself transmit them.

## Technical data model changes

Fields:
- `res.partner.ubl_cii_format` **removed**, replaced by `res.partner.invoice_edi_format` (extension of the base Accounting field). New computed `is_ubl_format`.
- `res.company.invoice_is_ubl_cii` and `res.config.settings.invoice_is_ubl_cii` **removed**.
- `account.move.send`: `enable_ubl_cii_xml`, `checkbox_ubl_cii_label`, `checkbox_ubl_cii_xml` **removed**; model moved to `wizard/`, now an AbstractModel. New `_get_alerts`.
- `res.partner._get_ubl_cii_formats()` now returns a list; the country mapping moved to `_get_ubl_cii_formats_by_country()`; new `_get_suggested_invoice_edi_format()`, `_peppol_eas_endpoint_depends()`.

Method signatures (14 modified, 45 added, 21 removed):
- `res.partner._get_edi_builder(invoice_edi_format)` (now a model method taking the format).
- `account.move._need_ubl_cii_xml(ubl_cii_format)`.
- `account.move.send._link_invoice_documents(invoices_data)` and `_get_placeholder_mail_attachments_data(move, extra_edis=None)` — batch-oriented.
- Export: `_export_invoice(invoice, convert_fixed_taxes=True)`; `_get_tax_category_list(customer, supplier, taxes)`; `_get_tax_unece_codes(customer, supplier, tax)`; `_get_uom_unece_code(uom)`; `_validate_taxes(tax_ids)`.
- Import: `_import_fill_invoice_form` / `_import_fill_invoice_line_form` and their helpers are replaced by a unified set (`_import_fill_invoice`, `_import_invoice_lines`, `_retrieve_line_vals`, `_import_partner`, `_retrieve_taxes`, `_find_value`, ...).
- Added export hooks: `_get_note_vals_list(invoice)`, `_get_tax_grouping_key(base_line, tax_values)`, `_get_invoice_line_item_vals(line, taxes_vals)`, `_get_customization_ids()`.
- New hooks on `account.move`: `action_invoice_download_ubl()`, `get_extra_print_items()`, `_get_invoice_legal_documents(filetype, allow_fallback=False)`.

## How your habits should change

- Stop relying on the company-level e-invoicing checkbox: set the format on each customer/vendor.
- In Send & Print, pick one communication channel per partner; batching reuses those defaults.
- Read the new alerts and complete Peppol EAS/Endpoint and recipient bank details before sending.
- If you have custom modules extending this addon, update overrides to the new method names and signatures (builders now receive the format instead of reading it from the partner).

## What you gain by migrating

- The correct e-invoice format is applied automatically per customer, including in batch.
- Fewer rejected or incomplete files thanks to pre-send checks and alerts.
- More reliable import of supplier e-invoices: better partner and bank matching, broader format tolerance.
- A cleaner foundation aligned with current Odoo and European e-invoicing practice, on a supported version instead of staying on 17.0.
