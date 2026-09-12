# base_vat migration guide (19.0 -> 20.0)

## What's new for users

In 19.0 the `base_vat` module carried everything VAT-related: offline number validation (format/checksum per country) **and** online VIES validation for EU intra-community numbers. In 20.0 that module has been dismantled and its responsibilities split:

- **Standard VAT validation now ships inside `base`.** It is available from the very first moment in a database, without installing any module, and behaves consistently before and after installing localizations.
- **VIES validation moved to a new, separate module: `l10n_eu_account_vies`** (Community). The "Verify VAT Numbers" option, the automatic EU validation of partner VAT numbers and its background checks now live there. Install it only if you need VIES.
- Localization (`l10n_*`) modules no longer need to depend on `base_vat`.

The official release notes contain no extract for this addon; the statements above come directly from the 20.0 source changes.

## Technical data model changes

Ground truth: 2 patches, 51 method signatures removed, 0 added, 0 modified.

Removed fields (previously added by `base_vat`):
- `res.company.vat_check_vies` (Boolean "Verify VAT Numbers")
- `res.config.settings.vat_check_vies` (related, readonly=False)
- `res.partner.vies_valid` (Boolean "Intra-Community Valid", stored and tracked)
- `res.partner.perform_vies_validation` (computed Boolean)
- `res.country.has_foreign_fiscal_position` (caching computed Boolean)

Removed code: all four model files of the addon (`res_partner.py`, ~990 lines, plus `res_company.py`, `res_config_settings.py`, `res_country.py`).

Removed methods include:
- Helpers: `_run_vat_checks`, `_check_vat_number`, `_format_vat_number`, `_build_vat_error_message`, `_get_vat_required_valid`, `_convert_hu_local_to_eu_vat`, `_ie_check_char`
- VIES: `_compute_vies_valid`, `_compute_perform_vies_validation`, `_check_vies_validity_iap`, `_check_vies_update_iap`, `_update_vies_status`, `_get_iap_vies_credentials`, `_get_iap_vies_endpoint`, cron `_cron_check_vies_validity_iap`
- ORM hooks: `create` and `write` (VIES recomputation handling)
- Country validators/formatters: `check_vat_*` (al, br, ch, cr, de, ec, gr, gt, hu, id, ie, il, in, jp, ma, mx, no, pe, ph, ro, rs, ru, sa, tr, ua, uy, ve, vn), `format_vat_*` (ch, cl, co, eu, hu, sm, vn), `is_valid_ruc_ec`
- Also removed from this addon by companion commits: `_inverse_vat`, `_onchange_vat` (partner identifiers unified behind a single JSON field) and `_split_vat` (generic VAT number method).

## How your habits should change

- Stop installing `base_vat` to get VAT checks: they run by default in 20.0.
- For EU intra-community checks, install `l10n_eu_account_vies` and enable "Verify VAT Numbers"; the VIES status on partners belongs to that module now.
- Update filters, exports, server actions, Studio fields and reports referencing `vies_valid`, `perform_vies_validation`, `vat_check_vies` or `has_foreign_fiscal_position` — these no longer exist as before.
- Any custom module that calls or overrides `check_vat_xx`, `format_vat_xx`, `_split_vat`, `_run_vat_checks`, `_inverse_vat` or `_onchange_vat` must be reworked against the new `base` / `account` implementations.
- Expect VAT validation to now apply also on databases where no localization is installed; re-test your partner import flows.

## What you gain by migrating

- VAT validation available globally from day one, with consistent behaviour regardless of installed localizations.
- A clear split between offline format validation and online VIES checks: you install only what you need, and can drop VIES without losing basic VAT control.
- A simpler dependency graph for localizations — no redundant `base_vat` dependency, fewer cross-module side effects.
- Less code to carry and upgrade: ~55 KB of VAT logic relocated to its proper home in `base` and `l10n_eu_account_vies`.
