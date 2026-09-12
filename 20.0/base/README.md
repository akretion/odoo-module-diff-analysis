# base migration guide (19.0 -> 20.0)

The official 20.0 release notes extract contains nothing specific to the `base` addon, so this guide is built from the actual code changes in `base` between 19.0 and 20.0. All items below concern the Community edition.

## What's new for users

- **Session-based device management.** Security no longer lists one line per device/browser combination. You now see one line per **session** (`res.session`), with the devices behind it available on demand. Revoking a session ends it cleanly and logs you out if it is your current one.
- **Simplified contacts.** The old "Person / Company" selector (`company_type`) is gone. A contact is treated as a company automatically when it is its own commercial entity and has a meaningful Tax ID. The form is shorter and less ambiguous.
- **Cleaner bank accounts.** Banks are no longer a separate catalogue: name, address, BIC/SWIFT, clearing/routing number and clearing label are entered directly on the account, alongside type and holder. Country-specific clearing rules (AU BSB, HK, JP, SA, US routing) are now validated in one place.
- **Currency rate date.** The multi-currency popover shows **Last Rate Update** next to the rate, so you can see how fresh the rate you are using is.
- **Partner identifiers.** National registry numbers (SIREN/SIRET, CVR, UEN, etc.) are unified behind a single flexible structure (`additional_identifiers`), with shared validation and automatic deduction of related numbers.

## Technical data model changes

- **Removed models**: `res.bank`, `ir.access.token`. **Added models**: `clearing.label`, `res.session` (SQL view over `res.device`).
- **`res.partner`**: removed `company_type`, `company_name`, `company_registry_label`, `company_registry_placeholder`. `is_company` is now a stored computed field (depends on `vat`, `commercial_partner_id`); new `additional_identifiers` JSON field plus helpers (`_get_all_identifiers`, `_validate_identifier`, ...). `_display_address` now uses `without_name` / `separator`.
- **`res.partner.bank`**: `acc_number` -> `account_number`, `acc_holder_name` -> `holder_name`, `acc_type` -> `account_type`, `sanitized_acc_number` -> `sanitized_account_number`, `retrieve_acc_type` -> `retrieve_account_type`, `_search_acc_number` -> `_search_account_number`; new `bank_name`, `street`, `city`, `zip`, `state_id`, `country_id`, `bank_bic`, `clearing_label_id`, `clearing_number`, `show_clearing_number`; `unlink` no longer archives silently.
- **`res.users`**: new `session_ids`; `SELF_READABLE_FIELDS` / `SELF_WRITEABLE_FIELDS` class attributes and `action_show_rules`, `onchange` overrides removed.
- **`res.device` / `res.device.log`**: `platform`, `browser`, `device_type` become computed from `user_agent`; new `browser_version`, `browser_language`; `user_id` on the log model is a plain integer.
- **`res.currency`**: `date` field removed, replaced by `rate_date` ("Last Rate Update"); `_get_rates` now returns `(rate, date)` tuples and uses a dedicated optimized query with two unique indexes (`NULLS NOT DISTINCT`).
- **`ir.config_parameter`**: `get_param`/`set_param` replaced by typed accessors `get_str/get_int/get_float/get_bool` and `set_*`.
- **`ir.actions.act_window.views`** is now `fields.Json`; `ir.actions.client.params_store` is now `fields.Text`.
- **Binary fields now strictly require bytes** (no arbitrary Python values).
- **Method signature changes**: `ir.http` auth methods take a `routing` argument; `ir.actions.report` `wkhtmltopdf`-specific methods are generalized to `_run_pdf_engine` / `_run_image_engine` / `_get_pdf_engine`; `_merge_pdfs` default error handler changed; `ir.qweb` compilation methods take a `compile_context`; `ir.attachment._file_read/_file_write/_same_content` signatures changed.

## How your habits should change

- Stop choosing "Person" or "Company" on contacts: fill in the Tax ID and let Odoo decide; use the parent/child structure for contact persons.
- Enter bank information on the account itself instead of creating a bank record first; existing accounts are migrated with their address/BIC moved onto the account.
- Manage access from the **Sessions** view rather than a device list; revoke a session, not individual devices.
- Use typed configuration parameters in your customizations instead of string parsing with `get_param`.
- If you customized `res.bank`, `company_type`, or the old `wkhtmltopdf` report methods, an adaptation is required.

## What you gain by migrating

