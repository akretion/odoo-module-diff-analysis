# base migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release-note extract we were given contains no section specific to the `base` module (it mostly covers Enterprise features), so nothing from it is claimed here as a Community feature. Everything below is user-visible and comes from the code changes themselves.

- **Rights are unified**: Access Rights and Record Rules are no longer two separate concepts. Both are managed through a single "Access" model, so permissions are reviewed in one place instead of two.
- **Contacts**: the Person/Company switch disappears. A contact is considered a company when it is its own commercial entity and has a real Tax ID. The old "Company ID" (registry) field is replaced by an "Additional Identifiers" block, validated per country, which automatically fills in companion numbers.
- **Sessions instead of device lists**: each session shows the device you are actually using (platform, browser, type deduced from the user agent) plus the other devices attached to it.
- **Bank accounts**: the shared bank directory is gone; bank name, BIC and address are entered on the account itself, with country-aware clearing/routing validation (AU, HK, JP, SA, US).
- **Multi-currency**: the rate table now displays the last rate update date.

## Technical data model changes

- `ir.model.access` and `ir.rule` are **removed**, replaced by `ir.access`. Removed fields: `model_access_ids`, `rule_ids` (`ir.model`), `model_access`, `rule_groups` (`res.groups`); `res.groups` gains `access_count`. `ir.model.access.check()` is deprecated (use `Model.has_access()`).
- `res.partner`: removed `company_registry`, `company_registry_label`, `company_registry_placeholder`, `same_company_registry_partner_id`, `company_type`, `company_name`. Added `additional_identifiers` (Json) and `available_additional_identifiers_metadata`. `is_company` becomes a stored computed field (own commercial entity + non-void VAT); `vat` gets an inverse.
- `res.company`: `company_registry` / `company_registry_placeholder` removed; `additional_identifiers` and `available_additional_identifiers_metadata` added.
- `res.bank` is deleted (new `clearing.label` model). `res.partner.bank` renames: `acc_number`→`account_number`, `sanitized_acc_number`→`sanitized_account_number`, `acc_holder_name`→`holder_name`, `bank_id` (m2o)→`bank_name` (Char), `bic`→`bank_bic`, `acc_type`→`account_type`, `country`→`country_id`, `state`→`state_id` (`country_id` is computed/stored).
- `res.device.log`: `platform`, `browser`, `device_type` (selection) and `linked_ip_addresses` removed; `user_agent` added, `user_id` becomes an Integer. New `res.session` model over `res.device`; `res.device` keeps computed `platform`, `browser`, `browser_version`, `browser_language`, `device_type`. `res.users` gains `session_ids`.
- `res.currency`: field `date` removed, `rate_date` added; `_get_rates()` now returns `(rate, date)` tuples.
- `ir.access.token` model removed (access-token API reverted).
- Binary-to-Json conversions: `ir.actions.act_window.views` is now Json; `ir.actions.client.params` Json and `params_store` Text.

## How your habits should change

- Stop looking for two separate "Access Rights" / "Record Rules" screens: both live under the new Access model. Filters, exports, dashboards or data imports referencing `ir.model.access` or `ir.rule` must be re-pointed.
- Stop filling "Company ID" on contacts: use the identifiers block. Entering a VAT now deduces companion identifiers (e.g. BE VAT → BCE/KBO, FR SIRET → SIREN). Remove any reliance on `company_type` / `company_name` in filters, imports or automated actions.
- Bank account imports/exports and templates change column names (`acc_number`, `bank_id`, `bic`, …). Bank names become free text per account.
- Revoke/list **sessions** rather than individual devices.
- Scripts, reports or integrations calling `_get_rates()` must unpack the new tuple; reading `rate_date` replaces the old `date`.
- Do not store Python data in `Binary` fields anymore — use Json/Text.

## What you gain by migrating

- One coherent permission model: simpler audits, no more mismatch between ACLs and record rules.
- Cleaner contact data: legal identifiers centralised, validated and cross-deduced, instead of scattered free-text columns.
- Self-contained bank accounts: no bank directory to maintain, country-appropriate clearing-number checks, fewer payment errors.
- A security screen end users actually understand (sessions with device detail).
- Faster currency-rate lookups with a visible last-update date, plus a stricter data model where Binary fields only hold bytes.
