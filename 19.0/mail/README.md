# mail migration guide (18.0 -> 19.0)

## What's new for users

The 19.0 release notes extract provided contains no Mail/Discuss-specific item for the Community edition. The user-visible changes below therefore come from the addon patches and affect daily use of chatter, Discuss, activities and link previews.

- Done activities are always kept. Marking an activity as done now archives it instead of deleting it, so activity history and reporting keep completed activities. The “Keep Done” option on activity types has been removed.
- Link previews are reused by URL. Odoo keeps one preview per unique URL and links it to every message containing that URL, reducing database size and external requests.
- Chat window state is stored in the browser. The open/folded/closed state of chat windows is no longer saved on the server nor synced across devices or sessions; it is local per browser, with correct window ordering.
- Scheduled-message BCC helper fields are gone (`notified_bcc`, `show_notified_bcc`).
- Discuss channel member data is computed more efficiently (`self_member_id`, `invited_member_ids`).

## Technical data model changes

- New model `mail.message.link.preview`:
  - `message_id` (required M2o, cascade), `link_preview_id` (required M2o, cascade, indexed), `sequence`, `is_hidden`, `author_id` (related to message author).
  - Unique index on `(message_id, link_preview_id)`; not-null constraints on both M2o fields.
- `mail.link.preview`: removed `message_id` and `is_hidden`; added `message_link_preview_ids` (O2m to `mail.message.link.preview`, restricted to `base.group_erp_manager`); added unique index on `source_url`; removed `_gc_mail_link_preview` autovacuum.
- `mail.message`: removed `link_preview_ids`; added `message_link_preview_ids` (O2m, `base.group_erp_manager`); added `_field_store_repr`.
- `mail.activity.type`: removed `keep_done`.
- `mail.activity`: `_action_done` now always archives activities instead of unlinking them; activity formatting no longer exposes `keep_done`.
- `discuss.channel.member`: removed `fold_state` and `_channel_fold`; added `_to_store_persona`; `_rtc_join_call` signature updated.
- `discuss.channel`: added computed `self_member_id` (M2o, `compute_sudo`) and `invited_member_ids` (O2m, `compute_sudo`); `is_member` now `compute_sudo`; `_get_or_create_chat` no longer accepts `force_open`; `_to_store` now takes `fields`.
- `mail.scheduled.message`: removed `notified_bcc` and `show_notified_bcc`; removed `_compute_notified_bcc`.
- Many mail models now explicitly declare `_name` (ORM requirement): `fetchmail.server`, `mail.activity`, `mail.activity.mixin`, `mail.alias`, `mail.message`, `mail.thread`, etc.
- Discuss Store API: `Store.one`/`Store.many` replaced by `Store.One`/`Store.Many` relation classes; `_to_store` is now optional and takes a `fields` argument.

## How your habits should change

- Treat done activities as archived records, not deleted ones. Use feedback/archive flows; reports may now include previously removed “done” activities.
- Do not expect chat windows to follow you across devices or logins. Open/fold state is local to the browser; cross-tab remains the supported sync case.
- Link previews are shared by URL. Hiding a preview still hides that message-link, but the underlying preview record may be reused elsewhere.
- Remove customisations based on `notified_bcc`, `show_notified_bcc`, `keep_done` or `fold_state`; those fields no longer exist.
- Developers: use `Store.One`/`Store.Many` and the new `_to_store(store, fields)` signature; `_to_store` is only needed for custom additions.

## What you gain by migrating

- Less database growth and fewer external HTTP requests thanks to URL-based link preview reuse.
- A clearer activity history: completed activities are always kept for reporting and audit.
- Simpler, more predictable Discuss chat-window behaviour with local state and correct ordering.
- Better performance and maintainability: batched prefetching, `compute_sudo` member fields, cleaner Store API, dead code and fields removed.
- All the above are core `mail` Community changes; no Enterprise-only feature is required to benefit from them.
