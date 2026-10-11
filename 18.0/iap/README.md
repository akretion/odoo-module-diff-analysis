# iap migration guide (17.0 -> 18.0)

## What's new for users

The official Odoo 18.0 release notes contain no section dedicated to the `iap` addon (the service-account layer behind SMS, partner autocomplete, IAP enrichment, etc.). The user-visible changes below come from the code shipped in 18.0.

- **SMS account registration from your own database.** You no longer have to finish setting up an SMS account on the IAP website: verify your phone number, set a sender name and configure email alerts directly in Odoo.
- **Email alerts are now per user.** Instead of one warning email address, you pick one or more Odoo users ("Email Alert Recipients"). They are warned when credits run low, in their own language.
- **A visible account state**: Unregistered, Registered or Banned.
- **Balance and account information are refreshed from IAP when you open the form**, and the service of an account already registered on IAP is locked (no accidental edits).
- **Validation on save**: the alert threshold must be positive and every recipient must have an email address.
- The "show token" toggle is gone; the account token stays hidden.

## Technical data model changes

New persistent model `iap.service`: `name`, `technical_name` (readonly, unique), `description` and `unit_name` (translated), `integer_balance`.

Removed model: `iap.account.info` (transient), with its `account_token`, `balance`, `unit_name`, `account_uuid_hashed`, `description`, `warn_me`, `warning_threshold` and `warning_email` fields.

`iap.account`:
- `service_name` (free Char) becomes `service_id` (required Many2one on `iap.service`); `service_name` and `description` become related fields. New `service_locked` boolean. The record name is now `name`, defaulting to the service name.
- `balance`: was computed from the transient info model; now a readonly Char filled from the IAP server.
- `warn_me` and `warning_email` are removed, replaced by `warning_user_ids` (Many2many `res.users`); `warning_threshold` becomes a stored float.
- New `state` selection and `validate_warning_alerts` constraint; `show_token` and `action_toggle_show_token` removed.
- Removed overrides/methods: `get_view`, `_compute_info`, `_compute_balance`, `_inverse_info`, `_search_info`, `get_services`.
- New: `web_read` (fetches account info from IAP unless the `disable_iap_fetch` context is set), `web_save`, `_get_account_information_from_iap`, `get_account_id(service_name)`.
- Server routes changed: `/iap/services-token` becomes `/iap/1/get-accounts-information`, and `/iap/update-warning-odoo` becomes `/iap/1/update-warning-email-alerts` (the payload now carries a list of `warning_emails` with email and language).
- `get()` resolves the service and raises a UserError for an unknown technical name.

## How your habits should change

- Configure alerts by **choosing users**, not by typing an email address; the old "warn me" checkbox disappears — the threshold plus the recipient list replaces it.
- Your alert configuration is now stored on the account in your own database and pushed to IAP when saved.
- Register your SMS sender name and phone number from Odoo instead of the IAP portal.
- Balance is informational and read-only; buy credits with the usual button.
- If you use OCA or custom modules that read `service_name` as a plain Char, extend `iap.account.info`, or rely on `get_services()`, they must be ported before going live.

## What you gain by migrating

- Faster, self-service SMS onboarding: no round-trip to the IAP website to register a sender.
- Alerts reach the right people, in their own language, and are persisted in your database rather than in transient records.
- A cleaner, constrained service registry (`iap.service`) instead of free-text service names: fewer configuration mistakes, and a service locked once the account is registered.
- Clear account state, refreshed balance and server-side validation, so credit issues are spotted before they block your SMS or enrichment flows.
- A future-proof foundation: the addon API is more explicit (`get_account_id`, single-account actions, dedicated service model) for your 18.0-and-beyond upgrades.
