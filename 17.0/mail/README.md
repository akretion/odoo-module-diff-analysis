# mail migration guide (16.0 -> 17.0)

## What's new for users

The official release notes cover many apps; the following Discuss/chatter items relate directly to this addon:

- Attachments panel in conversations: find every file exchanged in a channel or chat.
- Edit sent messages from the chatter.
- Recipients' email addresses are displayed when sending a message from the chatter, making mistakes easier to spot.
- Emoji selector with a "frequently used" section, and a GIF button to send GIFs to colleagues.
- Mark as unread: flag a message to process it later; the sender sees it as delivered, not seen.
- Mentions in the full composer, kept when switching from the small to the full composer.
- Pin messages, reachable from the conversation header.
- Search messages and notes posted in the chatter.

Other Discuss items of the release notes (voice messages, simultaneous screen/video sharing, raising hands in meetings, per-channel notification preferences) are not provided by this Community addon and are not covered here.

## Technical data model changes

**Mail templates**
- `report_template` (Many2one) becomes `report_template_ids` (Many2many, table `mail_template_ir_actions_report_rel`), with a domain limited to reports of the template's model.
- `report_name` is removed: the generated file name now comes from each report's own configuration.

**Mail aliases**
- `alias_user_id` is removed. Records created through the mail gateway are attributed to the recognized sender, or to whoever `alias_defaults` sets (e.g. `user_id`). Bounces now go to the alias creator instead of an "owner".

**Tracking values**
- `mail.tracking.value.field_desc` and `field_type` are removed; label and type are read from the linked `ir.model.fields`.

**Shortcodes**
- `mail.shortcode.message_ids` and `mail.message.canned_response_ids` are removed (unused link).

**User settings**
- `res.users.settings` now lives in `base` and mail extends it; `res.users.res_users_settings_ids` and `res_users_settings_id` are removed.

**Email colors**
- New stored fields `res.company.email_primary_color` / `email_secondary_color`, split from document colors. Settings now expose these instead of `primary_color` / `secondary_color`.

**New models**
- `mail.activity.plan` and `mail.activity.plan.template`: activity plans usable on any mail-thread model (previously HR employees only).
- `mail.partner.device` and `mail.notification.web.push`: browser devices and queued payloads for Web Push notifications, with cron `ir_cron_web_push_notification`.

Discuss-specific code was also moved into `mail/models/discuss/`, and the PWA, Web Push and vCard features moved from Enterprise to Community. These are organizational changes without data impact.

## How your habits should change

- Templates: you may attach several reports to one template. File names come from each report's own print settings, so the template no longer controls report naming.
- Aliases: the "Owner" field is gone. Use alias default values (for instance `user_id` on the created record) to drive responsibility and notifications.
- Emails vs. documents: header and button colors are now configured separately in Settings. Changing document colors still updates email colors, as long as you have not customized email colors yourself.
- Activities: scheduling now goes through a wizard, on a single record or on a batch, with or without an activity plan.

## What you gain by migrating

- Schedule activities and activity plans in batch, on any document, from list views or the chatter.
- Attach multiple reports to a mail template instead of being limited to one.
- Web Push notifications in Community: browser notifications for chats, mentions and direct messages.
- A cleaner data model for tracking values, aliases and shortcodes, easing future upgrades and customizations.
- All the Discuss usability wins listed above (edit, pin, search, mark as unread, GIF, mentions).
