# account_peppol migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes contain no dedicated `account_peppol` section, so nothing brand new is announced for Peppol users there (the only Peppol item, GLN on delivery contacts, is flagged as available since 17.0). The real user-visible change in 20.0 is a vocabulary and setup cleanup:

- Your "Peppol ID" (EAS code + endpoint) is now called Routing Scheme + Routing Endpoint, a generic naming meant to serve any e-invoicing network.
- The "Advanced Peppol configuration" wizard is gone; Peppol is now configured from Accounting settings and the registration flow only.
- The deprecated self-billing options disappeared from the company settings.
- Endpoint format checks are unified: one validation applies instead of three different, partial ones.

## Technical data model changes

Renamed / reshaped fields
- res.partner: `peppol_eas` -> `routing_scheme`, `peppol_endpoint` -> `routing_endpoint`, plus a new computed `routing_identifier` ("scheme:endpoint"). The demo "odemo" scheme is kept.
- res.company: `peppol_eas` and `peppol_endpoint` removed (they were related to the partner); read/set them through `company.partner_id`.
- res.config.settings: `account_peppol_eas` / `account_peppol_endpoint` removed, replaced by `routing_scheme` / `routing_endpoint` (related to the company partner, editable).
- `available_peppol_eas` -> `available_routing_schemes`, and `_compute_available_peppol_eas` -> `_compute_available_routing_schemes`.

Removed fields / features
- `peppol_activate_self_billing_sending` and `peppol_self_billing_reception_journal_id`, with their compute/inverse methods.
- The `peppol.config.wizard` model and the buttons `button_open_peppol_config_wizard` and `button_peppol_register_sender_as_receiver`.
- Company-side endpoint handling: `_check_peppol_endpoint`, `_check_peppol_endpoint_number`, the PEPPOL_ENDPOINT_RULES / WARNINGS / SANITIZERS tables, `_sanitize_peppol_endpoint_in_values` and the create/write overrides.
- In base: `company_registry` is dropped; partner identifiers are consolidated in the `additional_identifiers` JSON field, with base helpers selecting the preferred legal, tax and routing identifier.

Method signature changes
- `_get_peppol_verification_state(routing_identifier, invoice_edi_format, process_type='billing', partner=None)`: the endpoint and EAS arguments are merged into one routing identifier.
- `_get_peppol_proxy_identification_info(routing_scheme, routing_endpoint)`.
- Registration now raises "Please fill in the company's routing identification." when `company.partner_id.routing_identifier` is empty.
- New `_compute_routing_scheme_endpoint` on res.partner, which still skips the partners of already registered companies.

## How your habits should change

- On contacts and in Accounting settings, fill in Routing Scheme and Routing Endpoint instead of "Peppol ID" / EAS + endpoint.
- Sending, receiving, participant verification and branch/parent detection keep working the same way; they now rely on the routing identifier.
- Do not expect Odoo to sanitize or block endpoints per country at company level anymore: validation now lives in base and is shared.
- If you used the advanced configuration wizard or the self-billing fields, plan the change: they are no longer available.
- If a report, import template, Studio field or custom module refers to `company_registry`, `peppol_eas` or `peppol_endpoint`, migrate it before the upgrade.

## What you gain by migrating

- One routing concept for all e-invoicing networks: less duplicated configuration, and an identifier reusable beyond Peppol.
- Cleaner, shorter settings: deprecated fields and the extra wizard removed, a single validation path.
- Native multi-ID support on contacts (consolidated `additional_identifiers`), aligned with what localizations such as Saudi Arabia and Türkiye now use.
- Multi-company Peppol still works: parent/branch detection is preserved through the routing identifier.
