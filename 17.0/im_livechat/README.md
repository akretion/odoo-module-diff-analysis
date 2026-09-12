# im_livechat migration guide (16.0 -> 17.0)

## What's new for users

The official Odoo 17 release notes extract provided for this migration covers
"Industries" data modules (Lawyer, Bar and Lounge, Hairdresser, etc.). That topic
is unrelated to the Live Chat addon and is not part of this document.

In short: **the release notes say nothing functional about `im_livechat`**. There is
no new Live Chat feature, screen or setting announced for Odoo 17 in that extract.
The real change in this addon is an internal renaming of the conversation model —
it has no visible effect on how you run live chat sessions, but it does matter for
anyone who built reports, filters, automations or integrations on top of it.

Everything described below therefore comes from the source code changes only.

## Technical data model changes

Odoo 17 renames the "channel" model that carries both internal chat and live chat
conversations. The change comes from PR #118354 ("rename mail.channel to
discuss.channel"), done in preparation for splitting the `discuss` and `mail`
modules.

**Models renamed**

- `mail.channel` → **`discuss.channel`**
- `mail.channel.member` → **`discuss.channel.member`**

`im_livechat` continues to extend these models (`rating.mixin` + the channel model),
but now inherits from the renamed ones.

**Fields renamed / re-pointed**

- `chatbot.message.mail_channel_id` → **`chatbot.message.discuss_channel_id`**
  (still a required Many2one on the conversation, cascade delete). The record
  display name of `chatbot.message` follows the new field.
- `discuss.channel.chatbot_message_ids` (One2many to `chatbot.message`) now points
  to `discuss_channel_id`.
- `im_livechat.channel.channel_ids` ("Sessions") is now a One2many on
  **`discuss.channel`** instead of `mail.channel`.

**Method and API renames**

- `_get_livechat_mail_channel_vals` → **`_get_livechat_discuss_channel_vals`**
- `_open_livechat_mail_channel` → **`_open_livechat_discuss_channel`**
- Chatbot step/script methods `_post_welcome_steps`, `_validate_email`,
  `_chatbot_prepare_customer_values`, `_is_last_step`, `_process_answer`,
  `_process_step`, `_process_step_forward_operator` now take a `discuss_channel`
  argument instead of `mail_channel` (signature semantics only).
- The rating hook now checks `res_model == 'discuss.channel'` and opens
  `im_livechat.discuss_channel_view_form` instead of `mail_channel_view_form`.

**Bus / technical identifiers**

- Bus events published by live chat changed name:
  `mail.channel/new_message` → **`discuss.channel/new_message`**, and
  `mail.channel/unpin` → **`discuss.channel/unpin`**.
- Raw SQL used for inactive-session cleanup and operator load now targets the
  `discuss_channel` table and `model = 'discuss.channel'`.
- Python files renamed: `mail_channel.py` → `discuss_channel.py`,
  `mail_channel_member.py` → `discuss_channel_member.py`.

Existing conversations, chatbot messages and ratings are carried over by Odoo's
standard upgrade scripts (see odoo/upgrade#4553): no data loss is expected.

## How your habits should change

- Day-to-day use of live chat, chatbots and rating requests is unchanged.
- If you use the **Developer/Technical** menu, or write filters, server actions,
  automated actions or exports, replace `mail.channel` with `discuss.channel` and
  `mail.channel.member` with `discuss.channel.member`. Old names no longer exist.
- Any custom report, dashboard or SQL view referencing the `mail_channel` table,
  the `mail_channel_id` column on chatbot messages, or the bus events
  `mail.channel/new_message` / `mail.channel/unpin` must be updated.
- Third-party or in-house modules that inherit `mail.channel` must be migrated to
  `discuss.channel` before the upgrade, otherwise they will fail to load.
- Studio/automation rules built on the old model names must be re-created.

## What you gain by migrating

- A supported, upgrade-safe live chat addon on a current Odoo line, with the
  `discuss.channel` naming that all downstream Odoo 17 modules now expect.
- Cleaner code base alignment: Live Chat now lives on the same conversation model
  as Discuss, which is the foundation Odoo built for future chat features.
- Predictable maintenance: no orphaned references to the removed `mail.channel`
  names in views, bus channels or SQL.
- Migrating now, with Akretion, means your customizations and reports are adapted
  once, correctly, instead of being patched under pressure later.
