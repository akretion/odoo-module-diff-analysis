# mail migration guide (18.0 -> 19.0)

## What's new for users

The provided release notes extract does not list Community-specific changes for the `mail` addon; most listed features belong to other apps (Accounting, eCommerce, AI, Sign, Sales, etc.). The user-visible changes below come from the 18.0 -> 19.0 code changes for `mail`.

- **Done activities are always kept.** Marking an activity as done now archives it, so completed activities remain available for views/reporting. The per-activity-type “Keep Done” option is removed.
- **Link previews are shared by URL.** A URL preview is fetched and stored once, then reused by any message containing that URL. Hiding a preview is now per message.
- **Chat window state is browser-local.** Open/folded/closed chat windows are no longer stored on the server per member; they are kept in the browser.
- **Scheduled-message composer cleanup.** Obsolete BCC-related composer fields are removed.

## Technical data model changes

- **New model `mail.message.link.preview`** links messages and link previews. Fields: `message_id`, `link_preview_id`, `sequence`, `is_hidden`, related `author_id`. Unique constraint on (`message_id`, `link_preview_id`).
- **`mail.link.preview`**: removed `message_id` and `is_hidden`; added `message_link_preview_ids`; `source_url` is now unique.
- **`mail.message`**: `link_preview_ids` replaced by `message_link_preview_ids` (One2many to `mail.message.link.preview`).
- **`mail.activity.type`**: removed `keep_done`.
- **`mail.activity`**: `_action_done` now always archives done activities instead of unlinking or conditionally archiving. `activity_unlink` still unlinks; `activity_feedback` archives.
- **`discuss.channel`**: added computed sudo fields `self_member_id` and `invited_member_ids`; `is_member` is now also `compute_sudo`.
- **`discuss.channel.member`**: removed `fold_state`; removed `_channel_fold`; `_get_or_create_chat` no longer accepts `force_open`.
- **`mail.scheduled.message`**: removed `notified_bcc` and `show_notified_bcc`.
- **ORM cleanup**: many `mail` models now declare an explicit `_name`; some `_inherit` lists were changed to strings. No user-facing model rename.
- **Internal Store API refactor**: `_to_store` handling now uses `Store.One` / `Store.Many` classes, can be optional, and receives a `fields` argument.

## How your habits should change

- **Activity cleanup:** completed activities are no longer deleted by default and are not controlled by a “Keep Done” flag. Expect a longer activity history. Use `activity_unlink` only when you truly want to delete activities.
- **Link previews:** hiding a link preview now affects that message only. Other messages using the same URL still show the shared preview.
- **Discuss:** do not expect the same open/folded/closed chat windows to follow you across browsers, devices or sessions. They are local to the browser.
- **Scheduled messages:** old BCC display fields no longer exist; rely on standard recipient/follower behaviour.

## What you gain by migrating

- **Better activity history and reporting**, because done activities are preserved instead of being deleted.
- **Less database duplication and fewer external requests for link previews**, since each URL is cached once and reused.
- **Simpler Discuss state management**, with chat window state handled locally in the browser.
- **A codebase aligned with Odoo 19 ORM conventions**, making future maintenance and Community/OCA module compatibility easier.
