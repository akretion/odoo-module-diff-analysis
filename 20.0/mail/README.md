# mail migration guide (19.0 -> 20.0)

Scope: the `mail` addon (Discuss, Chatter, activities, tracking) from Odoo 19.0 to 20.0. No 20.0 release-note extract was available for this addon, so the functional points below come from the code changes shipped in this version.

## What's new for users

- **Polls in Discuss.** Any channel or chat can host a poll with predefined answers, optional multiple choice, live result percentages and an optional end date. Guests can vote, and expired polls close and post their result automatically.
- **Pin messages everywhere.** Pinning was limited to Discuss channels; it now works in every record's Chatter, so key messages can be pinned and retrieved later without scrolling.
- **Simpler "Seen" marks.** The intermediate "delivered/fetched" step is gone. One grey check = at least one member has seen the message; double purple checks = seen by all members.
- **Starred becomes Bookmark.** Message starring is renamed to bookmarking, backed by a bookmark box.
- **Simpler activity chaining.** "Trigger Next Activity" and the recommended-activities list are removed. An activity type now suggests a single next activity, and "Done & Schedule Next" opens the standard Schedule Activity popup (activity plans included).

## Technical data model changes

New models: `mail.poll`, `mail.poll.option`, `mail.poll.vote` (question, options, votes per user or guest, unique vote per option); `bus.sync.mixin` (generic bus synchronisation, used by discuss channel/member/category); `mail.track.mixin` (value tracking extracted from `mail.thread`, which now inherits `bus.listener.mixin` + `mail.track.mixin`); `res.role`.

Field changes:
- `mail.message`: `starred_partner_ids` -> `bookmarked_partner_ids`, `starred` -> `is_bookmarked`; new `has_poll`, `started_poll_ids`, `ended_poll_ids`, `mark_as_unread`. The `mail_poll` message type was added then dropped: polls are ordinary comments carrying a `has_poll` flag.
- `discuss.channel.pinned_message_ids` and `discuss.channel.member.fetched_message_id` removed; pinning now goes through `mail.thread.set_message_pin()`, and Chatter exposes `has_pinned_messages` / `pinned_messages`.
- `mail.activity`: `recommended_activity_type_id`, `previous_activity_type_id`, `has_recommended_activities` and `chaining_type` removed.
- `mail.activity.type`: `triggered_next_type_id`, `chaining_type` and the `suggested_next_type_ids` many2many become a single `suggested_next_type_id`, also added on activity plan templates (replacing `next_activity_ids`).

API / signature changes: `message_post()` accepts `partner_cc_ids` (CC recipients) and `tracking_values`; `activity_schedule()` accepts `activity_user_id_fname`; the `_notify_*` and `_track_*` helpers lose their `msg_vals` argument and are renamed (`_track_log`, `_track_post_template`, ...); the `_to_store` API is replaced by `_store_*_fields(res)` helpers; `ir.config_parameter.set_param()` is replaced by `set_bool` / `set_str` / `create` / `write`; `mail.template._parse_partner_to` -> `_parse_partner_list_ids`; `discuss.channel._create_group(partners_to -> users_to)` and `_broadcast(partner_ids -> users)`. Existing overrides must be reviewed.

## How your habits should change

- Create polls from the Discuss composer; expect them to close on their own at the end date.
- Pin/unpin from the message action menu on any record, not only in channels.
- Stop waiting for the "delivered" tick: only "seen" states remain.
- Rename your vocabulary: starred messages are now bookmarks (data moved by the standard upgrade scripts).
- Review every activity type that used "Trigger Next Activity" or several suggested activities: only one suggestion survives, and the next activity is no longer created automatically - users go through the scheduler popup.
- If you maintain custom modules, budget time for the method renames above.

## What you gain by migrating

- Higher engagement: polls turn Discuss into a lightweight decision tool.
- Faster information retrieval: pinning in Chatter plus the bookmark box.
- A consistent, easier-to-teach activity flow: one suggestion, one scheduling popup, plans usable everywhere.
- Cleaner internals for your customisations: `bus.sync.mixin` and `mail.track.mixin` remove hand-written glue code, and access-rights/search helpers were unified - lower risk and cheaper maintenance on the long run.
- A supported version: 19.0 still receives fixes, but new features and long-term security coverage happen on 20.0.
