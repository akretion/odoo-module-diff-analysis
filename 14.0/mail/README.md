# mail migration guide (13.0 -> 14.0)

## What's new for users

The official 14.0 release notes contain **no new user-facing feature for the `mail` module itself**. Everything they advertise lives in apps built on top of it (Email Marketing, SMS Marketing, CRM, Project, IAP) and is only available if you install those apps.

For a plain `mail` installation, the only user-visible evolution is clearer wording in the notification model:

- recipients of a notification are now labelled **"Recipient"** instead of "Needaction Recipient";
- the delivery status field is now labelled **"Status"** instead of "Email Status" (values unchanged: Ready to Send, Sent, Bounced, Exception, Canceled).

Everything else in this guide is internal clean-up.

## Technical data model changes

No model was added, removed or renamed. `mail.message`, `mail.mail` and `mail.notification` keep the same names and tables.

- **Python class renamed**: `Notification` -> `MailNotification` for the model `mail.notification`. The model name and the database table (`mail_message_res_partner_needaction_rel`) are untouched, so no data migration is required. Custom code importing or inheriting the class by its Python name must be updated.
- **Two fields flagged as deleted, then re-added**: `mail_id` (Many2one to `mail.mail`) and `read_date` (Datetime). They were only moved inside the file, not dropped: the net data model is identical. `mail_id` gained a help text ("Optional mail_mail ID. Used mainly to optimize searches.").
- **Label changes on existing fields**: `res_partner_id` string `Needaction Recipient` -> `Recipient`; `notification_status` string `Email Status` -> `Status`.
- `res_partner_id`: the explicit `required=False` was removed; the field is still optional.
- Fields regrouped by purpose (origin / recipient / status) plus cosmetic formatting of the index creation in `init()`; the index itself is unchanged.
- `create`, `write`, `format_failure_reason`, `_gc_notifications` and the SQL constraint keep the same behaviour; **no method signature change** was reported for this addon.

## How your habits should change

- Day-to-day use of the chatter, followers, activities, email templates and notifications is unchanged.
- Only if you filter, group or report on notification fields should you review wording relying on "Needaction Recipient" / "Email Status".
- Developers maintaining custom modules on `mail.notification` must rename the inherited class and re-check overrides of the methods listed above (signatures unchanged, but the file layout moved).
- Improvements advertised for Email Marketing, SMS Marketing, CRM and IAP in the 14.0 notes require installing those apps; do not expect them from a `mail`-only upgrade.

## What you gain by migrating

- **Low-risk migration**: no field genuinely removed, no table change - your messages, notifications, followers and activities move as they are.
- **Clearer data model**: notifications are grouped logically and labels are more readable, making delivery troubleshooting (bounce, exception) easier.
- **Better maintainability**: the module now follows 14.0 code conventions (license headers, ordered fields), limiting surprises with third-party or custom mail addons.
- **Foundation for 14.0 apps**: you get the 14.0 platform base (OWL framework, new search panel options, the `NOW` domain key, improved email template testing) that newer Email and SMS features rely on.
