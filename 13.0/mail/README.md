# mail migration guide (12.0 -> 13.0)

The `mail` addon powers Chatter, Discuss and every notification in Odoo.

## What's new for users

- **Files**: drag & drop files directly in Chatter or Discuss.
- **Notification history**: the Discuss menu lists the history of all your notifications.
- **Incoming emails**: CC recipients are now registered and can be added as followers of a document.
- **Actions**: a warning icon appears on kanban/list views when an action is required on a record.
- **Notifications**: a more modern notification system, also available in the front end.
- **Translations**: preview a mail in another language.
- **Ownership**: documents created by the incoming mail gateway are now attributed to the user matched on the email address, instead of the generic gateway user.

## Technical data model changes

Field renames and removals:

- `ir.model.fields.track_visibility` (Selection On Change/Always) is replaced by `tracking` (Integer): `True` = tracked with default order 100, an integer sets the display order in Chatter. The old parameter is still honoured.
- `mail.tracking.value.track_sequence` becomes `tracking_sequence` (oldname preserved).
- `mail.notification.is_email` is removed, replaced by `notification_type` (Selection `inbox`/`email`, required, default `inbox`); `email_status` becomes `notification_status`.
- `mail.notification.res_partner_id` is no longer required (a constraint keeps it mandatory for inbox/email).
- `mail.blacklist.mixin` is renamed `mail.thread.blacklist` and now inherits `mail.thread`; `message_bounce` moves from `res.partner` into this mixin, which now overrides `_message_receive_bounce`.
- `res.partner` inherits `mail.thread.blacklist` instead of `mail.blacklist.mixin`.
- `mail.channel`: `image` and `image_64` are removed; only `image_128` (128x128) remains.
- `mail.message.res_id`, `mail.followers.res_id` and `mail.activity.res_id` become `Many2oneReference` fields; a new `mail.message.mail_ids` One2many links messages to sent emails.
- `mail.address.mixin.email_normalized` is relabeled "Normalized Email".

Method signature changes:

- `message_post` and `message_notify` lose `model_description` and `mail_auto_delete`; extra keyword arguments are split between message fields and notification parameters.
- `_notify_thread(message, msg_vals=False, **kwargs)`; inbox and email notifications are handled by `_notify_record_by_inbox` and `_notify_record_by_email`.
- `_notify_customize_recipients` is removed.
- `mail.followers._get_recipient_data(records, message_type, subtype_id, pids, cids)` has a new `message_type` argument.
- `_notify_failure_update` becomes `_notify_mail_failure_update`.
- Template helpers move to `odoo.tools`: `format_tz` becomes `format_datetime`, and `format_date`/`format_amount`/`format_datetime` accept an optional `lang_code`.

## How your habits should change

- Configure tracking with the single `tracking` field (True or a sequence) instead of `track_visibility` + `track_sequence`.
- In email templates, replace `format_tz(...)` with `format_datetime(...)`; for `format_date` the keyword is `date_format`, not `format`. Use `lang_code` to force a language.
- Any report, filter or customization on `is_email`/`email_status` must use `notification_type`/`notification_status`.
- Custom modules using the blacklist must inherit `mail.thread.blacklist`; bounce counters come from that mixin.
- `_message_receive_bounce` no longer increments a `message_bounce` field automatically: implement it on your model if needed.
- Channel images are managed through `image_128` only.
- Technical note: `datas_fname` is removed from `ir.attachment`.

## What you gain by migrating

- Simpler, Studio-friendly tracking: one parameter to enable and order tracked fields in Chatter.
- A notification model ready for multi-channel messaging (inbox, email, and Odoo's SMS app).
- Sensibly faster Chatter and Discuss on large databases, thanks to the 13.0 ORM optimizations.
- Consistent bounce and blacklist handling for all mailing-enabled models.
- Documents created from incoming emails are attributed to their real author.

Migration is not only a renaming exercise: plan a review of your email templates and of any customization touching notifications or tracking.
