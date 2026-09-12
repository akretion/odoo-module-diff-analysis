# account_edi_ubl_cii migration guide (19.0 -> 20.0)

## What's new for users
The official 20.0 release notes contain no section relevant to this addon (and no Enterprise-only feature is claimed here). The user-visible changes below are derived from the code diff of the addon.

- **Renamed and unified e-invoicing address fields.** "Peppol ID" (EAS) and "Peppol Endpoint" become **Routing ID** (scheme) and **Routing Endpoint**, plus a new single editable field **EDI Routing Address** written as `SCHEME:ENDPOINT` (e.g. `0208:0123456789`). Filling the address fills the two other fields.
- **Country identifiers are now structured.** The generic `company_registry` field is gone; country-specific numbers (Belgian KBO/BCE, French SIREN/SIRET, Dutch KVK/OIN, German Leitweg-ID, Swedish, Norwegian IDs...) are kept as additional identifiers, and Odoo picks the most suitable one automatically for the legal entity, tax scheme and routing role.
- **Validation at save time.** Routing values are validated per scheme when you save a partner, with an explicit message naming the expected format instead of a generic "Peppol endpoint is not valid".
- **German B2G made easier.** XRechnung is selected automatically when the customer has a Leitweg-ID (no more reliance on EAS `0204`).
- **Better import.** Importing a UBL/CII file recovers routing IDs and additional identifiers, can create the missing customer, retrieves bank details, and logs the operation.

## Technical data model changes
`res.partner`
- Removed: `peppol_eas`, `peppol_endpoint`, `available_peppol_eas`, `is_peppol_edi_format`, `_peppol_eas_endpoint_depends`, `_build_error_peppol_endpoint`, `sanitize_peppol_endpoint`.
- Added: `routing_scheme`, `routing_endpoint` (stored, tracked), `routing_identifier` (computed + inverse, `SCHEME:ENDPOINT`), `available_routing_schemes`, `_clean_routing_endpoint` (called from `create`/`write`), `_get_all_identifiers(enrich=)`, `_get_preferred_routing_identifier_vals`, `_import_retrieve_customer_from_routing_identifier`, `_validate_identifier_by_scheme`.

`res.company`: new related `routing_scheme`, `routing_endpoint`, `routing_identifier`.

`uom.uom`: new `_get_unece_code`, `_get_uom_from_unece_code`, `_compute_is_unece_code_supported`, `create`/`write` sanitizing.

`account.move`: `action_invoice_download_ubl` and `get_extra_print_items` removed; `_get_specific_tax(name, domain)` signature changed; new `_group_lines_by_tax`, `_ungroup_lines`, `action_group_ungroup_lines_by_tax`, `_post_process_link_to_purchase_order`.

`account.move.send`: new `_get_ubl_available_attachments`, `_display_attachments_widget`, `_needs_ubl_postprocessing`, `_get_move_constraints`.

EDI models: the `EAS_MAPPING` table is removed (483 method signatures added, 6 modified, 37 removed). Export is refactored into small per-node hooks (`_ubl_add_*`, `_cii_*`) and import into a shared pipeline (`_import_*_collected_values`, `_import_retrieve_customer_search_plan`). Changed signatures include `_import_partner(..., routing_identifier=...)`, `_import_product(partner, ...)`, `_retrieve_line_vals(record, ...)`, `_retrieve_taxes(..., tax_exigibility=None)`. CII now exports `FR_SIREN` as SpecifiedLegalOrganization instead of `company_registry`.

## How your habits should change
- Set the **Routing ID / Routing Endpoint** pair, or simply type the **EDI Routing Address** (`scheme:endpoint`) — the old Peppol fields no longer exist.
- Stop putting registry numbers in a generic field: record the country identifier (BE KBO/BCE, NL KVK/OIN, DE Leitweg-ID, FR SIREN...) so Odoo can reuse it as legal-entity, tax or routing identifier.
- Expect blocking validation errors on save: fix partner data (valid KBO/BCE, 10-digit company number, valid e-mail for `EM`, ≤50 characters) before sending documents.
- For German public buyers, just record the Leitweg-ID; XRechnung becomes the default format automatically.
- Any custom code, Studio automation or import template reading/writing `peppol_eas`, `peppol_endpoint`, `is_peppol_edi_format` or `company_registry` must be updated to the new names.

## What you gain by migrating
- Cleaner, better-named partner data: one routing address instead of scattered country hacks.
- Fewer rejected e-invoices: validation happens at data entry, with actionable error messages.
- Correct automatic format choice (including XRechnung via Leitweg-ID) and better UBL/CII import.
- Network-agnostic routing: the same fields serve Peppol and other e-invoicing networks, so future formats and schemes plug in without rework.
- A supported, maintainable base: 19.0 keeps the legacy fields and workarounds, 20.0 removes them.
