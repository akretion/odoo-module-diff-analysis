# mail migration guide (17.0 -> 18.0)

## What's new for users

- **Schedule messages and notes.** From the full chatter composer, the Send/Log button now has a "Send later" dropdown: pick a date and time and the message (or note) is posted automatically by a scheduled job. Scheduled messages appear in a new "Scheduled Messages" section of the chatter, hidden while empty. Their author can edit them and cancel them; users with write access on the record can cancel them too. A template that carries a schedule date now shows up in that section instead of being posted later on.
- **Canned responses move to Discuss.** They can be private or shared with specific groups, and you must now select them manually: typing `:shortcut` no longer inserts the content automatically.
- **Global notification settings.** A new Notifications menu under Configuration sets the default for all channels (All messages / Mentions only / Nothing) and lets you mute everything for a chosen period. Per-channel choices still override the default, and now include an explicit "All Messages" option.
- **Simpler dynamic placeholders.** Users who are not members of the Mail Template Editor group can insert simple placeholders from a dedicated button in the composer, with an optional default value.
- Other Discuss improvements listed in the release notes (persistent composer, new-message separator, copy message link, @everyone mention, sub-threads, portal chatter) also reach your users.

## Technical data model changes

- **New model `mail.scheduled.message`** (distinct from `mail.message.schedule`, which only delays notifications): fields `subject`, `body`, `scheduled_date`, `attachment_ids`, `model`/`res_id`, `author_id`, `partner_ids`, `is_note`, `notification_parameters`. Cannot be scheduled in the past; the target record cannot be changed afterwards; access follows `_mail_post_access` on the related record. Posting happens through the `ir_cron_post_scheduled_message` cron, as the creating user.
- **`mail.shortcode` removed, replaced by `mail.canned.response`** (`source`, `substitution`, `description`, `last_used`, plus `group_ids`, `is_shared`, `is_editable`). Create/write/unlink broadcast bus updates to the author, the current user and authorized groups.
- **Discuss channels:** the computed `is_chat` field is removed (use `channel_type`), `_channel_channel_notifications` and `_send_transient_message` are gone, and recipient computation is rewritten around per-member and user-level notification settings.
- **Notification settings:** `res.users.settings` gains `channel_notifications` and `mute_until_dt`, a `mute(minutes)` method and the `ir_cron_discuss_users_settings_unmute` cron. `discuss.channel.member.custom_notifications` gains the `all` value; default now comes from user settings. Discuss web-push notifications are restricted to web-push recipients.
- **New `bus.listener.mixin`** (extended by mail): `_bus_send`, `_bus_send_store`, `_bus_channel` and `_bus_send_transient_message`. `discuss.channel` and `discuss.channel.member` inherit it, and direct `bus.bus._sendone`/`_sendmany` calls are replaced.
- **Mail thread:** attachments coming from `mail.scheduled.message` are accepted at post time, and `scheduledMessages` can be requested by the web client.
- **Template rendering:** `_is_dynamic` becomes `_has_unsafe_expression` (and the two `_is_dynamic_template_*` helpers are renamed), the QWeb context flag `raise_on_code` becomes `raise_on_forbidden_code`, and `ir.qweb` now allows only a directive/expression whitelist. Inline placeholders accept a default with `|||`.

## How your habits should change

- To delay a message, compose it, open "Send later" and choose the date; then track, edit or cancel it in the chatter instead of expecting it to be sent instantly.
- Canned responses: pick them from the list (or share them through groups) rather than relying on automatic `:shortcut` substitution.
- Set your notification preferences once in Configuration, then adjust individual channels only when needed.
- Template editors keep full QWeb; everyone else is limited to whitelisted placeholders and defaults.
- Review your customizations: any code using `mail.shortcode`, `is_chat`, `raise_on_code` or the old `bus.bus._sendone` helpers must be adapted.

## What you gain by migrating

Better control over when emails leave Odoo, cleaner notification noise for teams, shareable canned responses, safer templates for non-technical users, more consistent real-time Discuss updates, and a maintained codebase on a supported version (with upgrade scripts covering the model renames).
