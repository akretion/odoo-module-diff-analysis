# iap migration guide (17.0 -> 18.0)

## What's new for users

No Odoo 18 release-note extract covers the `iap` addon (the In-App Purchase framework behind SMS, Snailmail, Partner Autocomplete and other paid services). The changes below come from the 17.0 -> 18.0 source diff.

- Account information is now fetched live: balance, registration state and service details are read from the IAP server when the account form is opened, instead of being cached in a temporary record.
- Email alerts use recipients chosen among your own Odoo users (Email Alert Recipients), each notified in their own language, instead of one free-text address.
- Saving is validated: the alert threshold must be positive and every recipient must have an email address on their user record.
- The "show token" toggle disappears and the account page is simplified.
- Services become proper records (IAP Service), so technical name, description, unit and balance format are shared configuration, and the service is locked once the account exists on the IAP side.
- The registration flow improved for IAP services such as SMS (phone verification, sender name, warning emails handled from your own database).

## Technical data model changes

Added model
- `iap.service`: `name`, `technical_name` (unique), `description`, `unit_name`, `integer_balance`.

Removed model
- `iap.account.info` (transient) is deleted.

`iap.account` fields
- Added: `service_id` (Many2one `iap.service`, required), `service_locked`, `warning_threshold` ("Email Alert Threshold"), `warning_user_ids` (Many2many `res.users`), `state` (banned / registered / unregistered).
- Changed: `service_name` and `description` are related fields; `balance` is a stored read-only Char refreshed from IAP.
- Removed: `account_info_id`, `account_info_ids`, `account_uuid_hashed`, `warn_me`, `warning_email`, `show_token`, and `_rec_name = 'service_name'`.

Methods (0 modified, 6 added, 9 removed)
- Added on `iap.account`: `_get_account_information_from_iap`, `get_account_id(service_name)`, `is_running_test_suite`, `validate_warning_alerts`, `web_read`, `web_save`.
- Removed on `iap.account`: `_compute_balance`, `_compute_info`, `_inverse_info`, `_search_info`, `action_toggle_show_token`, `get_account_url`, `get_services`, `get_view`.
- Removed on `res.config.settings`: `_redirect_to_iap_account`.
- `web_read` triggers the IAP fetch, `web_save` disables it; writing `warning_threshold` or `warning_user_ids` calls `/iap/1/update-warning-email-alerts` with each recipient's email and language; on create the account name defaults to the service name.

## How your habits should change

- Rename or re-describe a service through its IAP Service record; once an account is registered, its service is locked.
- Register alert recipients as Odoo users with an email rather than typing a raw address, and keep the threshold positive.
- Do not treat balance and state as stored data to filter or report on: they are read from the IAP server when the form opens.
- Any custom code using `iap.account.info`, `warn_me`, `warning_email`, `show_token` or `get_services()` must be rewritten around `service_id`, `warning_user_ids` and `_get_account_information_from_iap`.

## What you gain by migrating

- Fewer round trips to the IAP website: alert configuration and account follow-up happen inside your own database.
- A real service catalogue: services are data, not hardcoded strings, so descriptions, units and balance formatting stay consistent and translatable.
- Better alerting: several recipients, per-user language, and validation that catches mistakes early.
- Less dead code to maintain: the temporary `iap.account.info` model and its compute/inverse/search methods are gone.
