# mail migration guide (18.0 -> 19.0)

This guide covers the `mail` addon (Discuss, chatter, activities, email gateway, templates). It is written for functional users and for customers planning an 18.0 → 19.0 upgrade on the **Community** edition.

## What's new for users

The official 19.0 release notes we have cover Accounting, AI, eCommerce, Sales, CRM, Project, Documents and Sign. **None of them describes this addon**: the only chatter-related items (AI drafting of emails/chatter summaries, AI prompts in mail templates) are Enterprise AI features and are therefore *not* part of the Community scope of this document. So the useful functional news below comes from the code changes shipped in `mail` itself:

- **Activities**: a revamped activity UI with one-click reschedule actions (Today, Tomorrow, Next Week) and better plan summaries showing the next activities of a plan.
- **Archiving no longer deletes activities**: archiving a record keeps its planned activities; they are now only removed when the record is actually deleted.
- **Deleted messages stay visible**: a deleted message no longer disappears from the thread, its content is removed instead.
- **Chatter search filters**: you can filter messages in the chatter (notifications only, etc.) instead of scrolling.
- **Attachments**: PDF files now get a preview thumbnail, and voice/audio notes carry playback metadata.
- **Link previews** are reused per URL instead of being generated again for every message.
- **Discuss / live chat**: channel invitations can be sent by email, member history is tracked, and the chat window state is stored in the browser.
- **Email servers**: a user can now configure a *personal* outgoing mail server, with a usage limit; using another user's server is blocked. Out-of-office can be configured on the user and is applied when posting.
- **Fetchmail**: mail retrieval runs as a cron with visible progress and a batch limit (50 per run), and the old IMAP timeout workaround is gone.

## Technical data model changes

- `mail.scheduled.message`: **removed fields** `notified_bcc` and `show_notified_bcc` (obsolete composer/scheduler fields) and their compute method.
- `mail.message`: **`record_name` field removed** (now a computed `_compute_record_name`); `is_thread_message` replaced by `_is_thread_message` / `_is_thread_message_visible`; `_message_fetch` now takes `thread`, `is_notification`; `_to_store(store, fields, ...)` signature changed; `_author_to_store` and `_cleanup_side_records` removed; `fetch(field_names=None)`.
- `mail.presence`: **new model**; presence logic moved out of `bus` (`bus.presence`, `ir.websocket` presence methods removed) into `mail`.
- Recipients: `_message_get_default_recipients(with_cc=False, all_tos=False)`, new `_message_add_default_recipients`, `_partner_find_from_emails`, and **batch-enabled suggested recipients** (`_message_get_suggested_recipients_batch`, moved to `BaseModel`, new `no_create` / `primary_email` / `reply_discussion` options).
- Notifications: `_notify_get_reply_to(..., author_id=False)`, reply-to uses the real author name, `msg_vals=False` defaults, `force_record_name` parameters, new out-of-office helpers.
- `mail.activity.mixin`: `activity_reschedule`, `activity_feedback`, `activity_search`, `activity_unlink` gain `only_automated=True`; new `action_reschedule_today/tomorrow/nextweek`; `toggle_active()` deprecated.
- `mail.tracking.duration.mixin`: new "rotting" fields and search/domain helpers.
- Attachments: limited-access ownership tokens (`_get_ownership_token`, `_has_attachments_ownership`) for deleting files, and mention tokens.
- `fetchmail.server`: `_fetch_mail(batch_limit=50)`, `fetch_mail()` (no more `raise_exception`), IMAP/POP3 helpers privatized.
- `mail.template`: `_generate_template(..., recipients_allow_suggested=...)`, new render/validation helpers, delete-confirmation modal removed.
- `res.partner`: `_find_or_create_from_emails` extended (`ban_emails`, `filter_found`, `no_create`, sorting); `_to_store` removed on `res.partner`.
- Globally: 72 signatures modified, 196 added, 91 removed.

## How your habits should change

- Stop expecting archiving to remove activities; delete records instead.
- Use the new chatter filters and reschedule shortcuts rather than editing dates by hand.
- Do not build views, exports or reports on `notified_bcc`, `show_notified_bcc` or `mail.message.record_name`: they no longer exist.
- Custom integrations calling `channel_create`, `channel_get`, `create_group` or `add_members` must adapt — these are now private/internal (`_create_channel`, `_get_or_create_chat`, `_add_members`).
