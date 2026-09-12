# sms migration guide (16.0 -> 17.0)

## What's new for users

The official 17.0 release notes extract available to us only mentions *Recruitment* (an Enterprise app) and its "send an SMS to several applicants from the list view" workflow. That is an application feature of another module and says nothing about the Community `sms` module, so it is not listed as a change here. The real user-visible changes come from the code:

Your SMS statuses now reflect what actually happened to the message.

- 16.0: an SMS was "Sent" as soon as Odoo's SMS provider accepted it. Nothing told you whether the message reached the phone.
- 17.0: a new **Delivered** status is reached only when the provider returns a delivery report. In between, the SMS is shown as **Sent** (accepted, waiting for the report) and **Processing** (in the provider's hands).
- Failures are much more detailed: unknown error, wrong number format, missing number, insufficient credit, unregistered account, server error, country not supported, country-specific registration required, expired, invalid destination, not allowed, not delivered, rejected.
- Some failures (invalid destination, not allowed, rejected) are now classified as **bounces**, so bad numbers can be spotted and cleaned.
- When a country requires registration or credits are missing, the error message now contains a direct link to register or to buy credits.

## Technical data model changes

New model
- `sms.tracker` ("Link SMS to mailing/sms tracking models"): `sms_uuid` (required, unique), `mail_notification_id` (m2o, cascade). It links a sent SMS (via its UUID) to its notification/mailing trace so provider feedback can update them.

`sms.sms`
- New `uuid` (Char, copy=False, default uuid4 hex, unique SQL constraint); new computed `sms_tracker_id`.
- `state` selection changed: `outgoing` (In Queue), **new** `process` (Processing), **new** `pending` (labelled "Sent"), `sent` now means **Delivered**, `error`, `canceled`.
- `failure_type` extended with `unknown`, `sms_country_not_supported`, `sms_registration_needed`, and delivery-report errors `sms_expired`, `sms_invalid_destination`, `sms_not_allowed`, `sms_not_delivered`, `sms_rejected`.

`mail.notification`
- New one2many `sms_tracker_ids`. `failure_type` gains `sms_country_not_supported`, `sms_registration_needed` and the delivery-report errors above. Notification statuses can now be `process`, `pending` and `bounce` in addition to the previous values.

`sms.api` / provider layer
- Endpoint changed from `https://iap-sms.odoo.com` to `https://sms.api.odoo.com`, send route from `/iap/sms/2/send` to `/iap/sms/3/send` (IAP API v3).
- `_send_sms(numbers, message)` was removed. `_send_sms_batch(messages, dlr=False)` has a new signature: messages are grouped by content, each entry carrying a list of `{uuid, number}`, and a `webhook_url` (your `/sms/status`) is sent when `dlr` is True.
- New error strings: `duplicate_message`, `country_not_supported`, `incompatible_content`, `registration_needed`.
- `_postprocess_iap_sent_sms` was removed; sending results are now applied through `sms.tracker` (`_update_sms_state_and_trackers`), and `action_set_canceled/error/outgoing` delegate to it.

## How your habits should change

- Re-read the status column: what you used to call "Sent" is now "Pending", and "Delivered" is what "Sent" used to mean. Saved filters, automated actions, reports or dashboards built on `state = 'sent'` must be reviewed — they now select delivered SMS only.
- For historical records the compromise is kept: an SMS stored as "Sent" is displayed as "Delivered"; old data is not recomputed.
- To get delivery reports, your Odoo database must be reachable from the Internet at `/sms/status` (webhook). If it is not, SMS simply stop at "Sent" — sending still works.
- When a message stays in "Sent"/"Processing", it is normal: the report has not arrived yet.

## What you gain by migrating

- Proof of delivery instead of a blind "sent" flag, plus early detection of unreachable numbers.
- Precise error reasons and one-click actions to register a country or buy credits.
- Bounce detection to keep contact lists and marketing campaigns clean.
- The current IAP SMS API v3, batched by message content: better performance and the foundation for `mass_mailing_sms` reporting.
