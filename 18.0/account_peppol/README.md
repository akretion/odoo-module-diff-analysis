# account_peppol migration guide (17.0 -> 18.0)

Between 17.0 and 18.0, `account_peppol` was reworked on three themes: sending and receiving are decoupled, partner verification is simplified, and Peppol plugs into the new Print & Send flow.

## What's new for users

- **Send without receiving.** You can register as a sender only and later upgrade to receiver. This matches the release note: *"Send invoices on the Peppol network while still receiving your bills in any other system."*
- **A registration wizard replaces the Settings form.** Onboarding (identity, phone/email, verification code, migration key) is now a wizard, launched from Settings or automatically from Send & Print when you send an invoice while not registered as sender.
- **Clearer statuses.** Demo/test/live selection and the old Not verified / Verification code sent / Pending / Active / Cancelled statuses become: Not registered, In verification, Can send but not receive, Can send (pending registration to receive), Can send and receive, Rejected.
- **Peppol services wizard** to review and configure the services linked to your Peppol account.
- **Preferred invoicing method per contact** (including "by Peppol"), pre-filling Send & Print.
- **Simpler, automatic partner verification.** One "Peppol endpoint verification" field per contact (Not verified yet / Not valid / Cannot receive this format / Valid), refreshed automatically and logged in the chatter.

## Technical data model changes

`res.company`
- `account_peppol_proxy_state` selection rewritten; `is_account_peppol_participant` removed; new `_check_peppol_purchase_journal_id`, `_peppol_supported_document_types`, `_peppol_modules_document_types`; new companies get a default `peppol_verification_state` property.

`res.partner`
- Removed: `account_peppol_is_endpoint_valid`, `account_peppol_validity_last_check`, `account_peppol_verification_label`.
- Added: `peppol_verification_state` (default `not_verified`, **company-dependent**: each company keeps its own result) and `invoice_sending_method` extended with `peppol`.
- `create`/`write` now trigger `_update_peppol_state_per_company`; `_compute_is_peppol_edi_format` depends on `invoice_edi_format` and the active company.
- Helpers refactored: `_get_participant_info`, `_check_peppol_participant_exists`, `_check_document_type_support`, `_log_verification_state_update`.

`account.move`
- `peppol_move_state`: `canceled` removed (cancelling resets the state to empty); `peppol_is_demo_uuid` removed; `send_and_print_values` replaced by `sending_data`.
- "Ready to send" now uses the company can-send domain and `is_sale_document(include_receipts=True)`.

`account.move.send` (new file)
- New hooks: `_get_alerts`, `_do_peppol_pre_send` (opens the registration wizard), `_is_applicable_to_company/move`, `_hook_if_errors` (marks failed moves as *skipped*), `_call_web_service_after_invoice_pdf_render` (sends after PDF/XML generation).

`res.config.settings` / `account_edi_proxy_client.user`
- Settings fields and buttons removed (verification code, endpoint warning, EDI mode, create/cancel registration); new `action_open_peppol_form`, `button_peppol_smp_registration`, `button_account_peppol_configure_services`.
- Proxy user gains `_call_peppol_proxy`, `_get_can_send_domain`, `_check_company_on_peppol`, `_peppol_register_sender_as_receiver`, `_peppol_deregister_participant`, `_peppol_get_services`, and a cron for `smp_registration`.

## How your habits should change

- Register through the wizard instead of Settings fields; the old "cancel registration" button is gone (deregister instead).
- Decide whether you only send or also receive; a sender can be upgraded later, and the migration key is only consumed when registering as receiver.
- Trust the contact verification state: sending fails with a clear message if EAS/endpoint are missing or the partner is not verified.
- In Send & Print, check the pre-selected channel rather than ticking everything; one preferred channel per partner is the intended pattern, and Peppol is pre-selected for valid partners with a Peppol format (UBL/BIS, not Factur-X).
- Rename saved filters or reports using the old status values.

## What you gain by migrating

- Flexibility: use Odoo as your Peppol access point to *send* invoices without committing to receiving them.
- Fewer errors and support calls: one verification field, automatic refresh and chatter traceability replace three inconsistent fields.
- Faster daily work: preferred sending methods per contact and a Send & Print that pre-selects Peppol, with alerts before sending to badly configured partners.
- Cleaner onboarding: a wizard you can open on the fly, and a "Can send and receive" status only when you really are on the network.
