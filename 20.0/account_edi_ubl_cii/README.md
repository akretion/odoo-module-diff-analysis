# account_edi_ubl_cii migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes barely mention this addon: UBL/CII e-invoicing work is mostly under the hood. Only two related items appear in the Accounting notes:

- Peppol global location identifiers (GLN) can be stored on partners' delivery contacts.
- Partner identifiers are now handled through the "multi-ID" feature on the contact form instead of dedicated/tag-based fields (this is the same underlying change mentioned in country sections such as Saudi Arabia and Türkiye).

Everything else below is derived from the addon itself.

## Technical data model changes

Four fields are removed from `res.partner`: `is_peppol_edi_format`, `peppol_eas`, `peppol_endpoint`, `available_peppol_eas`.

- `peppol_eas` becomes `routing_scheme`, `peppol_endpoint` becomes `routing_endpoint`, plus a new computed `routing_identifier` (`scheme:endpoint`). Routing is no longer Peppol-specific: any e-invoicing network can use these fields.
- `company_registry` is dropped entirely. Legal-entity, tax and additional identifiers now live in the JSON field `additional_identifiers`, which moved from `account` to `base` (so modules that do not depend on `account` can use it too).
- The hard-coded country/EAS table `EAS_MAPPING` is removed and replaced by shared base helpers: `get_tin_metadata_of_country()`, and partner helpers `_get_preferred_legal_entity_identifier_vals()`, `_get_preferred_tax_identifier_vals()`, `_get_preferred_routing_identifier_vals()`, `_get_additional_identifier()`. Country specifics (FR SIRET/SIREN, SG UEN, NO Brønnøysund, …) become non-stored compute/inverse fields.
- New identifier tooling (`ISO_6523_ICD_CODELIST`, `ISO_IDENTIFIERS_METADATA`, `normalize_iso_identifier`, `normalize_vat_for_ubl`) and a new `res_company` model file.
- Signature change: `_import_partner(..., peppol_eas, peppol_endpoint, ...)` becomes `_import_partner(..., routing_identifier=...)`.
- Import search plan: `_import_retrieve_customer_from_eas_endpoint` becomes `_import_retrieve_customer_from_routing_identifier`. An imported `EndpointID` now fills `routing_scheme`/`routing_endpoint` and also feeds `additional_identifiers`.
- XML generation now relies on those "preferred identifier" helpers instead of long per-country if/elif chains (BE, NL, LU, SE, DK, AU, NZ, NO…). `PartyLegalEntity/CompanyID` only carries a `schemeID` when the scheme belongs to the ISO 6523 ICD codelist.
- Factur-X/CII `SpecifiedLegalOrganization` is now emitted only for French SIREN/SIRET.
- Dutch compliance checks NL-R-003 / NL-R-005 now report the partner name instead of a generic missing-VAT message.

## How your habits should change

- Stop using `company_registry` for e-invoicing. Enter the legal-entity and tax identifiers in `additional_identifiers` on the contact.
- The Peppol-named fields are gone: set the routing scheme (EAS code) and endpoint through `routing_identifier`.
- Audit contacts before going live. Identifiers previously stored in `company_registry` or `peppol_*` must be migrated, and the preferred legal-entity/tax/routing identifiers must be filled in, otherwise the generated XML can lose identifiers (notably BE, NL, LU, SE, DK, NO, AU, NZ).
- For French CII/Factur-X, only SIREN/SIRET-based legal organization data is exported.
- When importing vendor bills, partner matching now uses the routing identifier rather than the Peppol EAS/endpoint pair.

## What you gain by migrating

- One consistent identifier model shared by UBL, CII/Factur-X, SAF-T and Peppol/PDP flows, with far fewer country-specific special cases.
- Coherent scheme and endpoint validation (three different, partial validations before).
- Network-agnostic routing fields, ready for e-invoicing networks beyond Peppol.
- `additional_identifiers` available at `base` level, so non-Account modules (payroll localizations, etc.) can use the same partner identifiers.
- More reliable partner matching on import and clearer compliance error messages that name the partner concerned.
