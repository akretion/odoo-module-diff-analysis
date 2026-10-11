# mail migration guide (18.0 -> 19.0)

## What's new for users

The 19.0 release notes do not list Community features specific to the `mail` addon; the visible changes below come from the module patches.

- Link previews are now stored once per unique URL and reused by every message containing that URL. Fewer external requests and much less duplicated data. A preview can still be hidden per message.
- Chat window open/folded state is stored in the browser, not on the server. Windows no longer follow you across browsers, devices or sessions; within one browser the state is local (and still synced across tabs).
- Activities marked as done are always archived, never deleted, so activity history and reporting keep them.
- Scheduled messages no longer show the "notified BCC"/"show BCC" fields.

## Technical data model changes

- `discuss.channel`: added computed `self_member_id` (Many2one `discuss.channel.member`, sudo) and `invited_member_ids` (One2many, sudo); `is_member` is now computed with `compute_sudo`.
- `mail.link.preview`: removed `message_id` and `is_hidden`; added unique index on `source_url` and One2many `message_link_preview_ids`. The `_gc_mail_link_preview` autovacuum and `mail.mail_link_preview_lifetime_days` parameter are gone; creation is throttled per domain instead.
- `mail.message`: `link_preview_ids` removed; replaced by `message_link_preview_ids` (One2many `mail.message.link.preview`).
- New model `mail.message.link.preview` (link preview <-> message): fields `message_id`, `link_preview_id`, `sequence`, `is_hidden`, related `author_id`; unique constraint on (message, preview).
- `mail.activity.type`: `keep_done` removed. `mail.activity._action_done` always archives completed activities.
- `discuss.channel.member`: `fold_state` removed, `_channel_fold` removed; `_get_or_create_chat` lost its `force_open` argument.
- `mail.scheduled.message`: `notified_bcc`, `show_notified_bcc` and `_compute_notified_bcc` removed.
- `_name` is now mandatory: models previously declared with `_inherit` only now state `_name` explicitly (e.g. `mail.activity`, `mail.thread`, `res.partner`, `fetchmail.server`). No functional change expected.
- Store/method signatures changed: `_to_store(store, fields)` plus `_to_store_defaults`; Store tuples became `Store.One`/`Store.Many` classes; `_bus_send_store` now accepts a field list or name; `_to_store_persona` added on members.

## How your habits should change

- Do not expect chat windows to reopen from the server on another browser/device; open state is per browser.
- Done activities remain in the history: use `activity_unlink` only when you really want to delete.
- Since link previews are URL-based, a known URL is reused without a new request; the old lifetime-days cleanup setting no longer applies.
- BCC notification display on scheduled messages is no longer available.
- Any customization or external integration relying on `link_preview_ids`, `fold_state`, `keep_done`, `notified_bcc` or old Store tuple syntax must be adapted before migrating.

## What you gain by migrating

- Less database space and fewer external HTTP calls thanks to shared link previews.
- Full activity history for audits, KPIs and reporting.
- Cleaner, more explicit models and a simplified Discuss Store layer, making future upgrades and customizations safer.
- Chat state kept client-side, reducing server writes on every chat open/fold.
