# mail migration guide (15.0 -> 16.0)

## What's new for users

The 16.0 release notes extract provided here has no section dedicated to the **mail** core module (Discuss and the chatter). The listed sections cover Email Marketing, Sign, Recruitment, Sales and Employees; several of those items are Enterprise-only or belong to other apps, so they are not claimed here as part of this migration. The user-visible changes below therefore come from the module's own code diff.

- Unread messages are now counted **per channel member** instead of per document/channel. Each user has their own unread badge in Discuss, and reading a channel only affects that member.
- The dynamic placeholder generator tab (the "Field / Sub-field / Default value" helper that built a copy-paste expression) was removed from email template forms, mass mailing and SMS.

## Technical data model changes

**Fields removed on `mail.thread`** (hence on every model using the chatter):
- `message_unread` (Boolean)
- `message_unread_counter` (Integer)
- the compute method `_compute_message_unread` was deleted.

**Field added on `mail.channel.member`:**
- `message_unread_counter` (Integer, computed, `compute_sudo`, depends on `channel_id.message_ids` and `seen_message_id`). It counts messages newer than the member's `seen_message_id`, ignoring notification-type messages.

**Model renamed:** `mail.channel.partner` -> `mail.channel.member` (table `mail_channel_partner` -> `mail_channel_member`). Data is migrated by the standard upgrade script; SQL constraints and indexes are renamed (`mail_channel_member_partner_unique`, `..._guest_unique`, `..._seen_message_id_idx`).

**Fields renamed:**
- `mail.channel.channel_last_seen_partner_ids` -> `channel_member_ids` (One2many, label "Members").
- `mail.channel.rtc.session.channel_partner_id` -> `channel_member_id` (required Many2one, ondelete cascade, one RTC session per member).
- `mail.channel.channel_partner_ids` keeps its name but its label changes from "Members" to "Partners" (it remains the res.partner view of the members).
- The Many2many relation used by `res.partner.channel_ids` and `mail.guest.channel_ids` is now `mail_channel_member`.

**Technical names changed:** bus notifications `mail.channel.partner/seen`, `/fetched`, `/typing_status` become `mail.channel.member/...`.

**Removed from `mail.render.mixin`** (used by templates, mailings and SMS): `model_object_field`, `sub_object`, `sub_model_object_field`, `null_value`, `copyvalue`, plus the onchange `_onchange_dynamic_placeholder`. These were non-stored fields, so no database column is lost. `_build_expression()` is still available for placeholders.

No method signature change was detected in this addon.

## How your habits should change

- To insert a dynamic value in a template, write the expression yourself, e.g. `{{ object.partner_id.name }}`. The guided generator is gone; already-saved templates keep working because their expressions are stored.
- Any custom view, filter, report, automation or Studio field based on `message_unread` / `message_unread_counter` on documents must be reworked: unread information now belongs to the channel member. The "Unread messages" indicator on records disappears.
- Custom code, connectors or JS widgets that use the `mail.channel.partner` model name, `channel_last_seen_partner_ids`, `channel_partner_id` on RTC sessions, or the old bus event names must be updated.
- For everyday Discuss users, behaviour is otherwise unchanged; unread badges are simply personal.

## What you gain by migrating

- **A maintained platform**: you leave a version past its maintenance window for 16.0 and its security and performance fixes.
- **Faster, more accurate Discuss**: unread counting is done directly in SQL per member, so posting a message no longer invalidates caches on every document, and each participant's badge is always right.
- **A cleaner data model**: the "channel member" naming is consistent across Discuss, live chat and website live chat, and matches what Odoo Enterprise and third-party modules expect, making your future customizations cheaper.
- **Less legacy code**: the placeholder builder and the duplicated unread logic are gone, reducing regression risk in your own customizations.
- The model/table rename and data move are handled by the standard upgrade script; we still recommend a functional test cycle, especially if you rely on live chat, Discuss channels or custom chatter widgets.
