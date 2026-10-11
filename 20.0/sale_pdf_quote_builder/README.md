# sale_pdf_quote_builder migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes contain no entry about product documents or the quotation PDF builder, so the user-facing changes below come from the addon's own code changes.

- **"Inside quote pdf" is gone.** Up to 19.0, a product document had to be flagged *Inside quote pdf* to be inserted in the quotation PDF. This module no longer adds that option to the *Attached on Sale* field: any product document that is a real PDF file can now be used in the quotation PDF.
- **The Quotation Builder lists all documents.** The document list is no longer pre-filtered on PDFs; the PDF-only filter is applied later, when the PDF is generated.
- **Documents are ordered around the quotation table.** Documents set to *Never* or *On Product Page* are inserted before the quotation lines; *From Quotation* and *On Order Confirmation* documents come after. Page headers stay first, page footers last.
- **Form fields are covered more widely.** The document field "Form Fields Included" is renamed "Fields", and it is now computed for every PDF document, not only for "inside quote" ones.
- **No more blocking errors.** The messages about URLs and non-PDF files being incompatible with an "inside quote" document are gone; such documents are simply not offered for quotations.
- After the upgrade, the maintenance job that assigns missing PDF form fields runs on *all* product documents, so pre-existing PDFs are covered too.
- Only if eCommerce is installed: the *Publish On Website* flag has been merged into *Attached on Sale*, so a single setting both prints the document with quotations and shows it on the product page.

## Technical data model changes

- `product.document.attached_on_sale`: this module no longer extends the selection with `("inside", "Inside quote pdf")` (1 selection value deleted, 0 added), including its `ondelete` mapping and help text.
- `product.document`: constraint `_check_attached_on_and_raw_compatibility` removed.
- `product.document.form_field_ids` (stored, computed): label changed to "Fields"; `_compute_form_field_ids` now parses every document whose raw content is a PDF (the `attached_on_sale == "inside"` condition is dropped).
- `sale.order.line._compute_available_product_document_ids`: domain changed from `attached_on_sale = "inside"` to `type = "binary"`, `file_size > 0`, `mimetype = "application/pdf"`.
- `sale.pdf.form.field._cron_post_upgrade_assign_missing_form_fields`: now searches all `product.document` records instead of the "inside" ones only.
- `ir.actions.report`: two new methods, `_get_product_documents_before_and_after_quote(documents)` and `_add_documents_to_writer(writer, line_documents_pairs, form_fields_values_mapping, order)`. The quote PDF is assembled as headers -> pre-quote product documents -> quote table -> post-quote product documents -> footers, using `(order_line, documents)` pairs (`order_line = None` for quotation documents). `_update_mapping_and_add_pages_to_writer` keeps its signature.
- No model added or removed; no other stored field is affected.

## How your habits should change

- Stop using "Inside quote pdf". Review the *Attached on Sale* value of your product documents after the migration and re-tag the ones that must keep printing with quotations (typically "From Quotation").
- Make sure the documents you expect in the PDF are real PDF files: binary content, non-zero size, PDF MIME type. URLs and other formats are ignored rather than raising an error.
- Expect more documents to expose a "Fields" tab and more form fields to be parsed; check your PDF form-field mappings after upgrading.
- For custom report code, calls to `_update_mapping_and_add_pages_to_writer` still work, but prefer the new `_add_documents_to_writer` and `_get_product_documents_before_and_after_quote` helpers.
- Re-check the reading order of long quotations: some product attachments now appear after the quotation lines instead of before them.

## What you gain by migrating

- Less configuration: one obsolete attachment option fewer to maintain, and no more "wrong document type" errors for users preparing quotations.
- Every PDF document attached to a product or variant becomes usable in the quotation builder, with the PDF filter applied safely at rendering time.
- A more predictable quote layout: cover pages first, quotation lines in the middle, manuals and terms afterwards.
- Automatic form-field detection on existing documents, thanks to the widened post-upgrade job.
- A cleaner, documented API for building quote PDFs, making report customizations easier to maintain.
