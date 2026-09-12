# account_peppol migration guide (19.0 -> 20.0)

## What's new for users

No Odoo 20.0 release-note extract matched `account_peppol`, so this section summarises the user-visible changes found in the code diff (an upgrade script is referenced upstream: odoo/upgrade#9187).

- **Peppol IDs are renamed to routing fields.** The old "Peppol ID (EAS)" + "Endpoint" pair is replaced by **Routing Scheme** + **Routing Endpoint** on partner and company forms, exposed together as a single routing identifier. The wording is network-neutral, so it also covers non-Peppol e-invoicing networks.
- **Simpler settings.** The separate "Advanced Peppol Configuration" wizard is gone, as is the deprecated "register sender as receiver" button. Configuration, deregistration and re-registration are done directly from the Peppol settings.
- **Peppol responses are handled in Odoo.** Incoming application responses can be listed from the invoice and answered: approval response, rejection wizard, and cancellation/reset of Peppol documents.
- **Self-billing settings removed.** The deprecated self-billing activation flag and reception journal have been deleted.

## Technical data model changes

Removed fields:
- `res.company`: `peppol_eas`, `peppol_endpoint`, `peppol_activate_self_billing_sending`, `peppol_self_billing_reception_journal_id`
- `res.partner`: `peppol_eas`, `peppol_endpoint`
- `res.config.settings`: `account_peppol_eas`, `account_peppol_endpoint`
- The advanced-config wizard actions (`button_open_peppol_config_wizard`, `button_peppol_register_sender_as_receiver`)

Added / changed fields:
- `res.partner`: `routing_scheme`, `routing_endpoint`, `routing_identifier` (computed), `peppol_response_support` (computed), `available_routing_schemes` (replaces `available_peppol_eas`)
- `res.config.settings`: `routing_scheme`, `routing_endpoint`, `peppol_participation_role`, `peppol_purchase_journal_required`, contact-email compute/inverse
- The dedicated endpoint rule/sanitiser tables and the `_check_peppol_endpoint` constraint were dropped; validation now follows the routing scheme/endpoint logic
- Partner identifiers are consolidated in the base `additional_identifiers` JSON field, with `company_registry` dropped

Method signature deltas: 5 modified, 59 added, 17 removed. Notable ones:
- `res.partner._get_peppol_verification_state(routing_identifier, invoice_edi_format, process_type='billing', partner=None)` (previously endpoint + EAS)
- `_check_document_type_support(..., partner=None)` and `_log_verification_state_update(old_value, new_value)`
- `account_edi_proxy_client.user._generate_webhook_token(company)`, `_get_peppol_proxy_types()`, `_get_peppol_proxy_endpoint(endpoint, proxy_type=None)`
- New response and registration methods: `account.move.action_open_peppol_reponses`, `action_peppol_open_rejection_wizard`, `action_peppol_send_approval_response`, `_peppol_send_response`, `_peppol_import_response`, `_cron_peppol_auto_register_services`
- `account.move._notify_by_email_prepare_rendering_context`: `msg_vals` replaced by `force_header` / `force_footer`
- New UBL/CII import logging helpers `_add_logs_import_invoice_ubl_cii` and `_log_import_invoice_ubl_cii` on `account.edi.common`

## How your habits should change

No release note describes this addon, so the following is inferred from the diff above.

- On partner and company records, fill **Routing Scheme** and **Routing Endpoint** instead of EAS/endpoint. Error messages now refer to "the company's routing identification".
- Forget the advanced configuration wizard: manage Peppol from the settings page, including deregister / re-register.
- For received or self-billed documents, work from the invoice: open the Peppol responses, approve or reject with the wizard, and reset or cancel documents when needed.
- Self-billing reception journal configuration no longer exists; if you relied on the deprecated flag, plan for it to disappear after the upgrade.

## What you gain by migrating

- One clear, network-neutral identifier concept on partners and companies, with fewer duplicate and legacy fields and more consistent validation.
- A real response workflow on Peppol invoices (approval, rejection, cancellation) directly inside Odoo.
- A cleaner registration lifecycle (deregister / re-register, parent-branch handling) and better import diagnostics.
- A code base ready for multi-network routing (PDP and other proxy types), so upcoming e-invoicing mandates require fewer structural changes.
