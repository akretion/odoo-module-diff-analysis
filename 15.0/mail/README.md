# mail migration guide (14.0 -> 15.0)

What changes in the `mail` addon (Discuss, chatter, activities) when moving from Odoo 14.0 to 15.0.

## What's new for users

Odoo 15 release notes, Discuss section:

- **Channels**: invite people as channel members; channel notifications have been simplified. Channels can no longer be followers, and mailing-list style mail channels are gone.
- **Chat members**: the list of members of a channel is now displayed.
- **Sidebar and usability**: channels, chats and direct messages are easier to find, with avatars in the DM / group DM section.
- **Direct messages**: create a group from an existing direct message.
- **Messages**: edit or delete a message, or add a smiley reaction.
- **Shortcut**: start a chat from the command palette (Ctrl+K).

The release notes say nothing else specific to this addon; everything below comes from the code changes.

## Technical data model changes

**Activities (mail.activity.type / mail.activity)**
- `force_next` removed; replaced by `chaining_type` — "Suggest Next Activity" or "Trigger Next Activity" — also available on activities.
- `default_next_type_id` → `triggered_next_type_id`; `next_type_ids` → `suggested_next_type_ids`; the two are mutually exclusive.
- `default_description` → `default_note`.
- Labels: "Scheduled Date" → "Schedule"; "after validation date" → "after completion date"; "Action to Perform" → "Action".

**Followers (mail.followers)**
- `channel_id` removed and `partner_id` is now required: only partners can follow a document. `name`, `email` and `is_active` become related fields on the partner, and the channel-related SQL constraints are dropped.
- Helper methods lost their channel arguments: `_get_recipient_data`, `_get_subscription_data`, `_insert_followers`, `_add_default_followers`, `_add_followers`.

**Messages and channels**
- `mail.message.channel_ids` removed: a message now belongs to a single document (`model` / `res_id`), channels included, which use `message_ids`.
- `mail.channel.channel_message_ids` removed.
- `mail.channel.email_send` removed: channels are chat only.

**Moderation removed**
- Model `mail.moderation` deleted.
- `mail.channel`: `moderation`, `moderator_ids`, `is_moderator`, `moderation_ids`, `moderation_count`, `moderation_notify`, `moderation_notify_msg`, `moderation_guidelines`, `moderation_guidelines_msg`.
- `mail.message`: `moderation_status`, `moderator_id`, `need_moderation`; `message_fetch` no longer takes `moderated_channel_ids`; the moderation API is gone.
- `res.users`: `moderation_channel_ids`, `is_moderator`, `moderation_counter`.

## How your habits should change

- Discuss channels are chats: they no longer send messages by email and cannot be moderated. Mailing lists and moderated email channels have to move to the separate module that replaces them.
- Only partners follow documents; you can no longer add a channel as a follower.
- No more moderation queue, "pending moderation" status, moderators or email allow/ban lists in Discuss.
- For activity types, pick the Chaining Type: "Suggest Next Activity" (types proposed to the user when the activity is closed) or "Trigger Next Activity" (the next activity is created automatically). Only one of the two lists applies.
- On activity types, "Default Description" is now "Default Note".

## What you gain by migrating

- A clearer Discuss: chat channels chat, documents get followers, and every message is attached to one record.
- Simpler, more predictable notifications, without channel self-following or email side effects inside chats.
- Explicit activity chaining through one setting instead of two overlapping fields.
- A supported version, with upgrade scripts that migrate the removed models/fields and renamed fields for you.
