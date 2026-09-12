# mail migration guide (17.0 -> 18.0)

The `mail` app is the backbone of Discuss and of every chatter in Odoo (sales, invoices, projects, recruitment...). Between 17.0 and 18.0 it received a large functional overhaul plus a deep internal rework of how data is sent to the web client. Below is what changes for you as a user, and what changes under the hood.

## What's new for users

**Canned responses reworked**
- Shortcodes become **Canned Responses**: private by default, shareable with specific **Authorized Groups**.
- You must now *manually pick* a canned response from the suggestion list instead of having it auto-substituted.
- Created/updated/deleted canned responses are pushed live to other users.

**Notification settings**
- New **Configuration > Notifications** menu: set your default for all channels (All Messages / Mentions only / Nothing) and mute notifications for a period or forever.
- Per-channel settings still override the global default.

**Discuss & chatter**
- **Sub-threads**: start a secondary discussion inside a channel from a message.
- **New message separator** above the last unread message; channels open at the last message you read.
- **@everyone** mention to ping all channel members at once.
- **Copy message link** to share a message in another thread.
- **Portal chatter**: portal users can react, copy links and edit/delete their own messages.
- **Message scheduling**: write a message in the chatter and schedule it for later.

**Activities**
- New **list view with mass actions** for activities (done, snooze, cancel, "done and redirect to next").
- **Activity plans: timing** — define the deadline of each plan step.

**Emails**
- **Dynamic placeholders** can be inserted with a dedicated button, and non *Template Editor* users may now use simple placeholders (e.g. `object.name`, `object.user_id.signature`).
- **Heavy attachments are turned into links** so mail servers no longer reject large emails.

## Technical data model changes

- **`mail.shortcode` removed → `mail.canned.response`**: same `source` / `substitution` / `description` / `last_used` fields, plus `group_ids`, `is_shared`, `is_editable`; `create`/`write`/`unlink` broadcast over the bus.
- **New model `mail.scheduled.message`** (checks, `post_message`, `_post_messages_cron`).
- **`discuss.channel`**: `is_chat` field removed; new constraints/methods for sub-threads (`parent_channel_id`, `from_message_id`, `_create_sub_channel`).
- **`discuss.channel.member`**: `custom_notifications` gains the `all` value; new `_mark_as_read`, `_set_new_message_separator`, `set_custom_notifications`, mute helpers.
- **`res.users.settings`**: new `channel_notifications`, `mute_until_dt`, `mute()`, `_cleanup_expired_mutes` (cron).
- **`mail.mail`**: attachments-as-links, `process_email_queue(batch_size=...)`, `send(post_send_callback=...)`, size estimation.
- **Web push**: `mail.notification.web.push` / `mail.partner.device` replaced by `mail.push` / `mail.push.device`.
- **`mail.message` / `mail.thread`**: access checks refactored (`_check_access`, `_get_with_access`, portal tokens); `_search()` no longer accepts `access_rights_uid`; the `_*_format` methods are replaced by `_to_store`/Store data.
- **`mail.template`**: new `send_mail_batch()`, batch `copy_data()`.
- **`mail.alias`**: `_sanitize_alias_name(name, is_email)`; alias-domain configuration migration.
- **`fetchmail.server.fetch_mail(raise_exception=True)`**; IMAP/POP connection helpers now need `server`, `port`, `is_ssl`.
- **`mail.render.mixin`**: dynamic templates are now checked against a whitelist of allowed expressions instead of blocking all code; error message reworded.

## How your habits should change

1. Stop relying on automatic shortcode substitution: open the canned-response menu and select one.
2. Move your default notification and mute preferences to the global Discuss configuration; use per-channel settings only for exceptions.
3. Check that your custom-made mail templates and aliases still render — the template security rule and alias sanitization changed.
4. Review custom modules touching `mail.shortcode` or the removed `_*_format` methods: they must be ported to `mail.canned.response` and `_to_store`.

## What you gain by migrating

- Cleaner, less noisy conversations: global notification control, mute, @everyone, sub-threads, unread separator.
- Faster daily follow-up thanks to the activities list view, mass actions and plan timing.
- Smarter emails: scheduled sending, dynamic placeholders for everyone, and large attachments delivered as links instead of bouncing.
- Shared, permission-aware canned responses for support and sales teams.
- A modernized, store-based client API that keeps Discuss responsive and ready for future versions.

