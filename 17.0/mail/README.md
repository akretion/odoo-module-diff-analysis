# mail migration guide (16.0 -> 17.0)

## What's new for users
Odoo 17 ships a large Discuss/chatter upgrade, available in Community:
- **Attachments panel**: find every attachment sent in a conversation.
- **Edit sent messages**, in Discuss and in the chatter.
- **Recipient email addresses** are displayed when sending a message from the chatter.
- **Emoji selector** with a "frequently used" section; **GIF button** to send GIFs to colleagues.
- **Mark as unread**: flag a message to process it later (the sender sees it as delivered, not seen).
- **Mentions in the full composer**: mention colleagues from the chatter's full composer; mentions are kept when switching from the small to the full composer.
- **Pin messages**: pin important or old messages; a pinned-message section in the conversation header lets you jump to them.
- **Search messages** and notes posted in the chatter.
- **Voice messages** to communicate with colleagues.
- **Notification preferences** per channel and conversation.
- **Username options menu**: all Discuss options are available when clicking a username in the chat header, plus access to the employee profile.

## Technical data model changes
**New models**
- `mail.alias.domain` (Email Domain): name, company_ids, bounce_alias/bounce_email, catchall_alias/catchall_email, default_from/default_from_email. It replaces the `mail.catchall.domain`, `mail.bounce.alias` and `mail.catchall.alias` system parameters. Each company has an `alias_domain_id`, so a multi-company database can have one domain per company (or several domains for one company).
- `mail.activity.plan` / `mail.activity.plan.template`: activity plans are no longer HR-only, they can target any mail.thread model.
- `mail.partner.device` and `mail.notification.web.push`: browser devices and queued web-push notifications.

**Added fields**
- `res.company`: alias_domain_id, alias_domain_name, bounce_email/bounce_formatted, default_from_email; email_primary_color and email_secondary_color (email colours are now separate from document colours).
- `mail.alias.alias_incoming_local` (boolean, default False): keep local-part-only matching for forwarded mail.
- `mail.activity`: date_done, attachment_ids, active; the state selection gains `done`.
- `mail.activity.type.keep_done`.
- `mail.template.report_template_ids` (many2many) replaces report_template/report_name.

**Removed fields**
- `mail.alias.alias_user_id` (owner): rely on alias defaults or the sender's identity.
- `mail.template.report_name` and `report_template` (report naming now comes from the report).
- `mail.tracking.value.field_desc` and `field_type` (computed from the field definition).
- `mail.message.canned_response_ids` and `mail.shortcode.message_ids` (unused link).
- `res.users.res_users_settings_ids/_id`: `res.users.settings` moved to `base`.

**Changed behaviour / signatures**
- Alias matching uses the full email by default (`sales@a.com` ≠ `sales@b.com`). Aliases flagged `alias_incoming_local` keep left-part matching; the upgrade sets this flag so forwarded mail keeps working, and `mail.catchall.domain.allowed` is still honoured for them.
- Bounce/catchall detection now iterates over all alias domains.
- `res.partner.get_mention_suggestions` no longer takes `channel_id`; use `get_mention_suggestions_from_channel(channel_id, search, limit)`.
- `mail.activity.get_activity_data(..., fetch_done=False)`: new parameter, activity types returned as dicts.
- `mail.tracking.value._format_display_value(field_type, new=True)`.
- `mail.alias._clean_and_check_mail_catchall_allowed_domains` → `_sanitize_allowed_domains`; `_set_alias_invalid` → `_alias_bounce_incoming_email(..., set_invalid=True)`.

## How your habits should change
- Fix a typo by editing the message, no longer by apologising for it.
- Open the attachments panel instead of scrolling back for a file.
- Configure notifications per channel instead of one global rule.
- Use "mark as unread" as a to-do list, and pin messages instead of bookmarking old exchanges.
- Search the chatter for past decisions instead of re-asking colleagues.
- Check the displayed email addresses before replying, to be sure of your audience.

## What you gain by migrating
- A clearly better Discuss and chatter experience, fully in Community.
- **Web Push and PWA**: install Odoo as an app (offline screen, app shortcuts) and get browser notifications even when the tab is closed — moved from Enterprise to Community in 17.
- **Multi-domain email**: one bounce/catchall domain per company and stricter alias routing; removing `alias_user_id` closes a spoofing path through the mail gateway.
- **Activity plans** on any model, batch scheduling, and "keep done" activities that retain history and attachments.
