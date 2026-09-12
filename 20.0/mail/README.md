# mail migration guide (19.0 -> 20.0)

## What's new for users
No official release-note extract covers the `mail` addon, so this guide is based on the code changes actually shipped in 20.0.

- **Cc recipients**: in the composer you now choose, per recipient, whether the message is sent in "To" or in "Cc". The notification popover shows which one was used, incoming Cc are processed, and mail templates gain a "Cc (Partners)" field next to "To (Partners)".
- **Polls in Discuss channels**: users and guests can vote on predefined answers; single or multiple choice, percentage computation, and an "end poll" message.
- **Pin messages in the Chatter**: pinning is no longer restricted to Discuss channels; it now works on any thread.
- **Simpler scheduled activities**: "Trigger Next Activity" and the recommended-activities list are removed. Each activity type defines a single "Suggest Next Activity", and "Done & Schedule Next" now always opens the activity scheduler wizard.
- **Server actions**: "Send Email" becomes "Send Message", a new "Log Note" action posts an internal note, and the "Send Email As" selector disappears.
- **Terminology**: "Starred" becomes "Bookmarked"; the "fetched" message indicator is removed, leaving a simpler seen state (one grey check: seen by some, two purple: seen by all).

## Technical data model changes
**Added models**: `mail.poll`, `mail.poll.option`, `mail.poll.vote` (Discuss polls, with one-vote-per-user/guest constraints), `mail.call.artifact` (post-call recording/transcript metadata; one attachment per artifact, non-overlapping segments), `bus.sync.mixin` (generic bus synchronisation) and `mail.track.mixin` (values tracking extracted from `mail.thread`).

**Added fields**: `mail.message.partner_cc_ids`, `mail.mail.recipient_cc_ids`, `mail.template.partner_cc`, `mail.scheduled.message.partner_cc_ids` (plus composer wizard fields), `mail.message.started_poll_ids` / `ended_poll_ids` / `has_poll`, `discuss.call.history.artifact_ids`, `ir.actions.server.log_note_note`, and `mail.message.is_bookmarked` / `bookmarked_partner_ids` (renamed from `starred` / `starred_partner_ids`).

**Removed or relocated**: `mail.tracking.value` moves out of `mail` into a dedicated `mail_tracking` module, and `tracking_value_ids` disappears from `mail.message`; `discuss.channel.member.fetched_message_id` and `channel_fetched()`; `discuss.channel.pinned_message_ids` (replaced by `mail.thread.set_message_pin`); `mail.activity.recommended_activity_type_id`, `previous_activity_type_id`, `has_recommended_activities`, `chaining_type`; `mail.activity.type.triggered_next_type_id`, `chaining_type` and `suggested_next_type_ids` (now a single `suggested_next_type_id`); `mail.activity.plan.template.next_activity_ids`; `res.users.settings.push_to_talk_key`, `use_push_to_talk`, `voice_active_duration`; `ir.actions.server.mail_post_method`.

**Behaviour and signatures**: the `mail_poll` message type was dropped — polls post as normal `comment` messages flagged by `has_poll`. `_track_execute` / `_track_post_template` now handle logging; `_action_done` and `action_feedback` no longer return the next activities; user settings are read through the settings record and synchronised by `bus.sync.mixin`; outgoing emails also resolve `recipient_cc_ids`, which adds a few queries when preparing mails.

## How your habits should change
- Recreate "trigger" activity chains as a single "Suggest Next Activity" per activity type: the next activity is no longer created automatically, the scheduler popup is always used.
- Recreate server actions that used "Send Email As: Note" as "Log Note"; "Message" ones become "Send Message".
- Adapt filters, reports or automations referring to `mail_poll` message types, `tracking_value_ids`, `fetched_message_id`, `pinned_message_ids` or the removed starred fields.
- If you rely on field tracking in the chatter, make sure the new `mail_tracking` module is installed and updated.
- Push-to-talk and voice-activity preferences are now per device (browser storage) instead of shared server-side.

## What you gain by migrating
- Full Cc support across composer, templates, gateway and notifications.
- Native polls for quick decisions inside Discuss, including guest participation.
- Pinning available in every chatter, plus a leaner and more reliable "seen" indicator.
- A lighter core: tracking is optional (`mail_tracking`), and reusable mixins (`mail.track.mixin`, `bus.sync.mixin`) simplify the custom modules you maintain.
- Fewer confusing options for end users in activities and server actions.
