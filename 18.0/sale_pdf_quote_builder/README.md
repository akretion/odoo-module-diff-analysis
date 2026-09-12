# sale_pdf_quote_builder migration guide (17.0 -> 18.0)

This addon merges PDF headers, footers and product documents into sale quotations and fills in their PDF form fields. Odoo 18 rebuilt it around reusable documents and per-quotation choices.

## What's new for users

- Custom zones: the quote builder is more flexible — choose when to use it, which documents to use, and insert text specific to each quotation in the PDF.
- Any field of the model, including fields you created yourself, can now be used to fill a PDF form field, with no development.
- Headers and footers become reusable records (with type, sequence, activation) that can be linked to quotation templates and selected per quotation.
- Product documents to merge are now chosen per sale order line instead of being taken automatically from the first eligible document.
- The "Send quotation" and "Order confirmation" email templates now mention the product documents available to the customer in the portal.

## Technical data model changes

New models:
- `quotation.document` (Header/Footer, inherits `ir.attachment`): `document_type`, `sequence`, `active`, `quotation_template_ids`, `form_field_ids`.
- `sale.pdf.form.field`: a form field found in a PDF; `name` (unique per document type), `document_type`, `path` (dynamic mapping, optional), linked `product_document_ids` / `quotation_document_ids`.

Removed:
- `res.company.sale_header(_name)`, `sale_footer(_name)`.
- `sale.order.template.sale_header(_name)`, `sale_footer(_name)`, and later the `sale_header_ids` / `sale_footer_ids` helpers, replaced by `quotation_document_ids`.
- `sale.order.is_pdf_quote_builder_active` (replaced by computed `is_pdf_quote_builder_available`).
- The dynamic-fields wizard action and the JSON config parameter that stored the field-path mapping.

Added / changed:
- `product.document.form_field_ids`, computed from the attached PDF.
- `sale.order`: `quotation_document_ids` (now manually editable, no longer computed/stored), `available_product_document_ids`, `customizable_pdf_form_fields` (JSON, manually editable), `is_pdf_quote_builder_available`.
- `sale.order.line`: `product_document_ids` is no longer defaulted by a compute; `available_product_document_ids` lists eligible documents.
- `sale.order.template.quotation_document_ids`.

Method changes (`ir.actions.report`):
- Added `_update_mapping_and_add_pages_to_writer`, `_get_value_from_path`, `_get_custom_value_from_order`.
- Removed `_fill_sol_documents_fields`, `_get_so_form_fields_mapping`, `_get_sol_form_fields_mapping`, `_get_form_fields_values_mapping`, `_get_formatted_field`; `_add_pages_to_writer` now takes a `prefix` instead of a `sol_id`.
- `product.document.action_open_pdf_form_fields` replaces the wizard action, and a post-upgrade job scans existing PDFs to create the matching form fields.

## How your habits should change

- Upload headers/footers once as Quotation Documents and pick them per quotation or quotation template; they are no longer stored on the company or template form.
- The "Use PDF Quote Builder" toggle and the document-selection wizard are gone. Selection happens directly on the quotation, and custom text is typed on the spot.
- Field mapping is no longer wizard- or parameter-based: it lives on `sale.pdf.form.field` records, where an empty path means "free text filled per quotation".
- Select explicitly the product document(s) to include on each order line; nothing is preselected for you.
- Form field names may only contain alphanumerics, hyphens or underscores and cannot start with `sol_id_`. Only unencrypted PDFs are accepted.

## What you gain by migrating

- More control over generated quotes: combine several headers, footers and product documents, decide per quotation, and personalise each one with free text.
- Any Odoo field, standard or custom, can feed a PDF form field: layouts evolve without a developer.
- Reusable, centrally managed documents with type, sequence and activation, instead of duplicated company and template binaries.
- A cleaner data model: existing PDF form fields are detected automatically during the upgrade.
- You also get Odoo 18's quotation-side improvements, such as product documents referenced in quotation emails.
