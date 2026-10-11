# base migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes focus on business apps and localizations and say almost nothing about `base`, the technical core addon (contacts, companies, users, currencies, security). This section is therefore short by design.

The one change the release notes do confirm for this addon is the partner "multi-ID" concept: the Saudi Arabia and Türkiye notes describe identifiers (MERSIS, national IDs) now managed through a "multi-ID feature on a partner's contact form" instead of tags or dedicated fields. That is the new unified identifiers block on contacts and companies.

Everything else you will notice — a simpler contact form, richer bank accounts, sessions instead of a list of devices — comes from the addon itself and is described below.

## Technical data model changes

Security (the biggest change)
- `ir.model.access` and `ir.rule` are replaced by one `ir.access` model that holds both permissions and record restrictions.
- `ir.model`: `model_access_ids` and `rule_ids` removed; `access_ids` now points to `ir.access`.
- `res.groups`: `model_access` and `rule_groups` removed; new `access_ids` and `access_count`; `comment` is relabelled "Notes".
- `ir.model.access.check()` is deprecated in favour of `Model.has_access()`.

Contacts and companies
- `res.partner`: new `additional_identifiers` (JSON) plus `available_additional_identifiers_metadata`; `vat` gets an inverse that validates and deduces companion identifiers.
- Removed fields: `company_type`, `company_name`, `company_registry`, `company_registry_label`, `company_registry_placeholder`, `same_company_registry_partner_id`, and `company_registry` on `res.company`.
- `is_company` is now computed and stored (from VAT and the commercial entity) instead of being freely set.

Banks
- New `clearing.label` model; the `res.bank` model is removed, bank details now live on `res.partner.bank`.
- `res.partner.bank` fields renamed: `acc_number` → `account_number`, `sanitized_acc_number` → `sanitized_account_number`, `acc_type` → `account_type`, `acc_holder_name` → `holder_name`; adds `country_id`, `clearing_label_id`, `clearing_number`. Deleting a bank account now archives it.

Sessions and devices
- New `res.session` model; `res.device` aggregates the devices seen inside a session and `res.device.log` becomes a technical log storing `user_agent`.
- `res.users` gains `session_ids`.

Others
- Access tokens: `ir.access.token` and the `_grant_access_token` / `_get_sudo_record_from_access_token` / `_revoke_access_tokens` API are reverted and removed.
- `res.currency`: `date` removed, new computed `rate_date`; `_get_rates()` now returns `{currency_id: (rate, date)}`.
- `ir.actions.act_window.views` becomes JSON (was Binary); `ir.actions.client.params` is JSON, `params_store` is Text.
- Profiling: new `ir.profile.query` model with an EXPLAIN ANALYSE action for system users.

## How your habits should change

- Security is edited in one place (`ir.access`). Custom modules, scripts or imports writing to `ir.model.access` / `ir.rule` must be rewritten.
- No more "Person / Company" selector on contacts: the type is deduced, so make sure the Tax ID and country are filled in.
- No more Company ID / registry field: use the identifiers block; VAT-derived numbers (e.g. FR SIREN from a SIRET) are filled automatically.
- No more separate bank directory: capture bank name, BIC and address on the account, use the clearing label/number for domestic routing, and archive rather than delete.
- Revoke sessions rather than individual devices on a user.
- Reports or EDI using `company_registry` or `peppol_eas`/`peppol_endpoint` must be adapted to the identifiers JSON and to the renamed routing fields.
- Developers: use `Model.has_access()` instead of `ir.model.access.check()`, and drop any access-token calls.

## What you gain by migrating

- Easier-to-audit security: permissions and record rules in a single model with one caching path.
- Cleaner contacts: one identifiers block covering VAT and local identifiers, with validation, normalisation and automatic deduction.
- Simpler bank accounts: bank info on the account, typed and validated clearing numbers per country, no orphan directory.
- A clearer view of who is connected: one line per session, with the devices behind it.
- Performance and visibility: faster currency-rate lookups, faster access-rights checks, the currency rate date in the multi-currency popover, and a native query-plan tool.
- Less legacy to maintain (`company_type`, `company_registry`, `res.bank`, access tokens).
