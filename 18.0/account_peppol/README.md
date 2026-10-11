# account_peppol migration guide (17.0 -> 18.0)

This addon connects Odoo to the Peppol e-invoicing network through the Odoo Access Point. Version 18.0 reworks registration, partner verification and the invoice sending flow. Everything below concerns the Community addon only.

## What's new for users

- **Send without receiving.** You can now register only to *send* invoices over Peppol, and decide later whether you also want to *receive* documents — the release notes phrase it as "Send invoices on the Peppol network while still receiving your bills in any other system." Receiving is now a separate, optional step.
- **A dedicated registration wizard.** Registration no longer happens inline in Settings. A wizard walks you through it, and the same wizard opens from Send & Print when you try to send a Peppol invoice while not yet registered as a sender.
- **Clearer Peppol statuses.** The company status is now: Not registered, In verification, Can send but not receive, Can send pending registration to receive, Can send and receive, Rejected.
- **Preferred invoicing method per contact.** You can define a preferred invoice-sending method (including "by Peppol") and e-invoice format on a contact, so batch sending pre-selects the right channel and format instead of you ticking boxes each time.
- **Partner verification is visible and traceable.** One "Peppol endpoint verification" state (Not verified yet / Not valid / Cannot receive this format / Valid) replaces the old validity flag and label, and changes are logged in the contact's chatter.

## Technical data model changes

`res.company`
- `account_peppol_proxy_state`: removed `not_verified`, `sent_verification`, `pending`, `active`, `canceled`; added `in_verification`, `sender`, `smp_registration`, `receiver` (`not_registered` and `rejected` kept).
- Removed `is_account_peppol_participant`; `account_peppol_phone_number` relabelled "Phone number".

`res.partner`
- Removed `account_peppol_is_endpoint_valid`, `account_peppol_validity_last_check`, `account_peppol_verification_label`.
- Added `peppol_verification_state` (Selection, stored, *company-dependent*, default `not_verified`).
- Added `invoice_sending_method` option `peppol`.
- `_compute_is_peppol_edi_format` now depends on `invoice_edi_format` (was `ubl_cii_format`) and on the company context.
- `create`/`write` now refresh the verification state per company.
- `_check_peppol_participant_exists(participant_info, edi_identification, check_company=False)`: new signature (participant info passed in, `ubl_cii_format` argument dropped).

`account.move`
- `peppol_move_state`: `canceled` removed; cancelling a Peppol document resets the state instead.
- `send_and_print_values` replaced by `sending_data`.

`res.config.settings`
- Removed `account_peppol_endpoint_warning`, `account_peppol_verification_code`, `is_account_peppol_participant`, `account_peppol_edi_mode`, `account_peppol_mode_constraint`, plus the old registration/verification/cancel buttons. The logic moved to the new `peppol.registration` wizard and to the EDI proxy user model.

`account_edi_proxy_client.user` and new `account.move.send`
- New helpers: `_get_can_send_domain`, `_call_peppol_proxy`, `_check_company_on_peppol`, `_peppol_register_sender_as_receiver`, `_peppol_deregister_participant`.
- New `account_move_send.py` plugs Peppol into the Send & Print framework: alerts, `_do_peppol_pre_send` (opens the wizard), `_is_applicable_to_company` / `_is_applicable_to_move`, `_call_web_service_after_invoice_pdf_render`.
- Cron jobs now filter on the new states (`receiver`, `smp_registration`, send-capable states).

## How your habits should change

- Register through the wizard, not from Settings, and finish by choosing whether you want to receive.
- If you only send, you stay in "Can send but not receive" and do not wait for SMP registration.
- "Cancel registration" no longer exists: deregister to return to Not registered. Migration keys now concern receivers and are consumed on use.
- Cancelling an unsent Peppol document clears its Peppol status (no more "Cancelled").
- Look at the contact's Peppol verification state rather than an endpoint-validity checkbox; use the contact's preferred sending method and e-invoice format to let batch sending pre-fill choices.

## What you gain by migrating

- Freedom to start sending on Peppol without opening yourself to inbound document flows — useful if you already receive bills through another channel.
- Far less friction to get started: a guided wizard, instant verification, and on-the-fly registration when you first send.
- Cleaner, more meaningful statuses for support and follow-up.
