# base_vat migration guide (18.0 -> 19.0)

## What's new for users

- One consistent VAT pipeline: the number is compacted (spaces/separators removed) and validated both while you type (onchange on VAT / Country) and when you save, instead of only on create/write.
- Country prefixes are only accepted when the contact's country is in the EU (plus a few exceptions). A European company may keep another EU country's VAT prefix; other prefixes are no longer silently kept.
- Formatting and validation only run when a Country is set on the contact: no country, no reformatting.
- Use "/" to explicitly state "no (valid) VAT". Any other single-character value now raises an explicit error instead of being silently skipped.
- Error messages are clearer: the expected format is shown only when a reference format exists for the country.
- The duplicate-partner warning compares VAT numbers with and without the country prefix, so the same number typed two ways is caught.
- VIES: the "perform VIES validation" flag and the VIES check now rely directly on the VAT field; the hidden helper field is gone.
- Japan: a "T"-prefixed number is validated as a Japanese VAT number (no more auto-detection from the country). Serbian VAT numbers are also validated now.
- Fiscal positions: the old "country group + foreign VAT" logic is removed; the standard rule (an EU VAT number may carry another EU country's prefix) now covers it.
- The app no longer requires Accounting: base_vat only depends on base_setup and mail.
- The 19.0 release notes also mention a new EU VAT territory country group excluding territories not subject to EU VAT, improving invoicing to those territories.

## Technical data model changes

Removed:
- `base_vat` no longer inherits `account.fiscal.position` (`models/account_fiscal_position.py` deleted: `adjust_vals_country_id`, `_validate_foreign_vat`, `raise_vat_error_message`, `_get_vat_valid`).
- Field `res.partner.vies_vat_to_check` (Char, computed).
- `_compute_vies_vat_to_check`, `fix_eu_vat_number`, the `check_vat` constraint and `check_vat_t`.

Added:
- `res.country.has_foreign_fiscal_position` (Boolean, computed, `@api.depends_context('company')`) — technical caching field, new `res_country.py` file.
- `res.partner.check_vat_jp` and `check_vat_rs`; `_split_vat` rewritten (2-character prefix, `('', vat)` when not alphabetic).

Changed:
- `res.partner.vat` and `country_id` are stored with `inverse="_inverse_vat"`; validation moved from `@api.constrains('vat','country_id')` to that inverse plus `@api.onchange('vat','country_id')` (`_onchange_vat`, `validation=False`).
- `_run_vat_test(vat_number, default_country, partner_is_company=True)` becomes `_run_vat_checks(country, vat, partner_name='', validation='error')`, returning `(vat, country_code)` and raising, blanking or skipping depending on `validation`.
- `simple_vat_check(country_code, vat_number)` becomes `_check_vat_number(country_code, vat_number)` (returns True when no checker exists for that country).
- `_compute_vies_valid` / `_compute_perform_vies_validation` now depend on `vat`.
- `_build_vat_error_message`: same signature, expected format now optional.
- Module dependency changed from `account` to `base_setup` + `mail`.

## How your habits should change

- Do not prefix a VAT number with a country code unless it is an EU (or exception) prefix; otherwise it is treated as part of the number or rejected.
- Always set the Country on the contact: without it, nothing is formatted or checked.
- To say "no VAT", enter exactly "/". A stray "-", "0" or "N/A" single character will now block the save.
- Expect your VAT to be rewritten in compact form; search and re-import with compact values.
- Japan: enter "T" then the digits. Switzerland accepts a space before MWST/TVA/IVA.
- Check your fiscal positions: if they relied on a country group plus a foreign VAT to decide VAT validity, review them, as that logic was removed.

## What you gain by migrating

One shared code path formats, validates and de-duplicates VAT numbers, so contacts stay consistent and errors surface immediately while typing. You get clearer messages, fewer silent failures, correct EU-prefix and VIES handling, and a base_vat that works without the Accounting app — a lighter, more predictable foundation for VAT compliance.
