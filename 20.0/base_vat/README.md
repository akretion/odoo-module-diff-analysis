# base_vat migration guide (19.0 -> 20.0)

## What's new for users

The 20.0 release notes extract contains no section dedicated to `base_vat`. The relevant changes are described in the source patches. In short, the two VAT validation features that used to live together in `base_vat` have been separated:

- **Standard (offline) VAT number validation** now lives in the core `base` module. It is available globally from the start, so basic VAT/company ID format checks work on contacts without installing an extra module.
- **VIES (EU intra-community) validation** now lives in a new optional module, `l10n_eu_account_vies`. The company setting formerly called "Verify VAT Numbers" (`vat_check_vies`) moved there. Only databases that need live VIES checks need this module.

`base_vat` is therefore retired as the owner of this logic: it no longer defines its own models, and localization modules no longer need to depend on it.

## Technical data model changes

Removed from `base_vat` by the 20.0 patch (no new fields or models are added):

- `res.company.vat_check_vies` — Boolean, "Verify VAT Numbers".
- `res.config.settings.vat_check_vies` — related setting for the above.
- `res.country.has_foreign_fiscal_position` — computed caching Boolean.
- `res.partner.vies_valid` — Boolean "Intra-Community Valid", stored, computed with `readonly=False`, tracked.
- `res.partner.perform_vies_validation` — computed Boolean deciding whether VIES status is relevant for a partner.

The whole `res.partner` implementation previously shipped by `base_vat` was removed, including:

- `_run_vat_checks`, `_check_vat_number`, `_build_vat_error_message`, `_get_country_specific_vat_variants`;
- VIES/IAP helpers: `_get_iap_vies_credentials`, `_get_iap_vies_endpoint`, `_check_vies_validity_iap`, `_check_vies_update_iap`, `_cron_check_vies_validity_iap`, `_update_vies_status`;
- country-specific `check_vat_*` overrides and reference VAT formats.

No method signatures are added by this addon. The standard validation hooks now belong to `base`; the VIES hooks belong to `l10n_eu_account_vies`.

## How your habits should change

- If your 19.0 database had "Verify VAT Numbers" enabled on the company, install/enable the new `l10n_eu_account_vies` module after migration to keep live VIES checks. Without it, EU intra-community validation no longer runs.
- If you never used VIES, you no longer need `base_vat` for standard VAT validation: entering a partner VAT number validates its format by default because the logic now ships in `base`.
- Any custom code, server action, import/export or report that reads `vies_valid`, `perform_vies_validation`, `vat_check_vies` or `has_foreign_fiscal_position` must be updated. Those fields no longer exist in 20.0.
- The stored `vies_valid` field is gone from this addon's data model. If you used it for fiscal-position rules, reporting or automation, plan a review and confirm with your integrator how existing VIES statuses are migrated, if at all.
- Localization modules no longer declare `base_vat` as a dependency, so install order and module lists are simpler.

## What you gain by migrating

- VAT format validation works from day one on any database, even before a localization or accounting module is installed.
- EU VIES checking becomes a deliberate opt-in: companies that do not trade under EU VIES rules avoid unnecessary `account` dependencies and related settings.
- Cleaner module graph: localizations stop declaring `base_vat` redundantly, reducing install conflicts and unnecessary coupling.
- One clear home for standard VAT checks (`base`), making future upgrades, customizations and support more predictable.
