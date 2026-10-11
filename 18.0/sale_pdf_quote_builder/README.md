# sale_pdf_quote_builder migration guide (17.0 -> 18.0)

## What's new for users

Odoo 18 reworks the PDF Quote Builder around "custom zones". Instead of a company-level header/footer plus a configuration wizard, the feature becomes flexible and per-quotation:

- Choose per quotation whether the builder is used, and which documents are included.
- Headers and footers are now standalone, reusable documents ("Headers/Footers"): upload a PDF once and attach it to any number of quotations. Documents can also be linked to quotation templates, so a template brings its own header and footer.
- Product documents are selected per quotation line, so you attach exactly the datasheet or terms needed for a given customer.
- Custom zones: each form field found in your PDFs can either be filled with text typed directly on the quotation (per-customer wording) or mapped to any field of the order, order line or product — including custom fields.
- The selection dialog only lists documents actually available for the quotation (based on template and sold products).

Other sales release-notes items (Shopee, Amazon, combo products, commission plans, pricelist revamp, portal loyalty, etc.) belong to other apps or to Enterprise and are not part of this addon.

## Technical data model changes

New models:
- `quotation.document` ("Quotation's Headers & Footers"), inheriting `ir.attachment`: `document_type` (header/footer), `sequence`, `active`, `quotation_template_ids`, `form_field_ids`.
- `sale.pdf.form.field`: form fields parsed from the PDFs. A `path` marks a dynamic field; without a path it is a customizable (free text) field.

Removed:
- `res.company` inheritance and the fields `sale_header`, `sale_header_name`, `sale_footer`, `sale_footer_name`.
- `sale.order.is_pdf_quote_builder_active` (replaced by the computed `is_pdf_quote_builder_available`).
- `sale.order.template.sale_header_ids` / `sale_footer_ids` (`quotation_document_ids` remains).
- The dynamic-fields wizard: `action_open_dynamic_fields_wizard` becomes `action_open_pdf_form_fields`.
- The system parameter `sale_pdf_quote_builder.form_field_path_mapping`: field paths now live on `sale.pdf.form.field` records.

Changed:
- `sale.order`: new `quotation_document_ids` (Many2many), `customizable_pdf_form_fields` (Json, user-editable), `available_product_document_ids` (computed). Without a template, the first header and first footer are preselected.
- `sale.order.line.product_document_ids`: no longer computed/stored; no default document is auto-selected.
- `product.document`: new computed, stored `form_field_ids`.
- `ir.actions.report`: `_add_pages_to_writer(writer, document, prefix=None)` replaces the previous `(writer, document, all_form_fields, sol_id)` signature; `_update_mapping_and_add_pages_to_writer`, `_get_value_from_path` and `_get_custom_value_from_order` replace `_get_form_fields_values_mapping` / `_get_formatted_field`.
- Generated form-field prefixes are now `quotation_document_id_X__` and `sol_id_X_product_document_id_Y__`.

## How your habits should change

- Stop uploading headers/footers on the company: manage them in the new Headers/Footers documents and reuse them across quotations and templates. The standard upgrade scripts convert your existing company documents — check the result after migrating.
- No more "activate the builder" toggle plus selection wizard: documents are edited directly on the quotation.
- Configure form fields from the document's "Form Fields" action, deciding for each whether it is dynamic (model path) or free text filled per quotation.
- Salespeople must now explicitly pick product documents per line: nothing is preselected anymore.
- A company-level header/footer is no longer applied automatically unless the corresponding document is available and selected.

## What you gain by migrating

- Reusable, centrally managed headers, footers and their fields: consistent quotes across salespeople.
- Quotation templates carry their own document sets, saving manual work on every quote.
- Any model field, including custom ones, can be used — no more JSON configuration parameter to maintain.
- Per-quotation custom text and per-line product documents give truly personalized quotes.
- A cleaner, supported data model aligned with future Odoo versions, and an easier upgrade path.
