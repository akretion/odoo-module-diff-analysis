# mail migration guide (19.0 -> 20.0)

_Scope: the `mail` addon, Community edition._

## What's new for users

**Cc recipients.** The composer, the recipient suggester and the notification popover now let you choose, per recipient, whether a mail goes in "To" or "Cc", and show which one was used.

**Polls in Discuss.** Channels accept polls with predefined answers, single or multiple choice, live percentages and closing dates.

**Pin messages in the Chatter.** Pinning was Discuss-only; it now works in any record's chatter.

**Bookmarks.** "Starred" is renamed "Bookmarked" in the UI.

**Clearer read receipts.** The "fetched" indicator is gone. Seen now means: one gray check = some members saw it, two purple checks = everyone saw it.

**Simpler activities.** "Trigger Next Activity" and the list of suggestions are gone; an activity type keeps a single "Suggest Next Activity". "Done & Schedule Next" always opens the scheduling popup (so plans can be used), pre-filled with that suggestion.

**Server actions.** "Send Email" becomes "Send Message"; a new "Log Note" action posts an internal note.

**Per-device call settings.** Push-to-talk shortcut and voice-activity duration are stored per device instead of being shared across all your devices.

**Tracking.** The tracking message is now generated on the fly instead of being stored as tracking values; admins who still need raw values can install a dedicated module.

Other release-note items (mailing click tracking, template library…) belong to the Email Marketing app, not to `mail`.

## Technical data model changes

New models: `mail.poll`, `mail.poll.option`, `mail.poll.vote`, `mail.call.artifact`; abstract mixins `mail.track.mixin` and `bus.sync.mixin`. `mail.tracking.value` moved to a dedicated module.

Added: Cc fields `mail.message.partner_cc_ids`, `mail.mail.recipient_cc_ids`, `mail.scheduled.message.partner_cc_ids`, `mail.template.partner_cc` ("To" partners stay in `partner_ids` / `recipient_ids`); `mail.message.has_poll`; `ir.actions.server.log_note_note`; a unique constraint on `ir.attachment` for call artifacts.

Renamed: `mail.message.starred_partner_ids` -> `bookmarked_partner_ids`, `starred` -> `is_bookmarked`.

Removed:
- `mail.message.tracking_value_ids`, and the `mail_poll` message type (polls post as `comment`).
- `mail.activity`: `recommended_activity_type_id`, `previous_activity_type_id`, `has_recommended_activities`, `chaining_type`.
- `mail.activity.type`: `chaining_type`, `triggered_next_type_id`, `suggested_next_type_ids`, replaced by one `suggested_next_type_id` (same on `mail.activity.plan.template`).
- `discuss.channel.member.fetched_message_id`, `channel_fetched()`, `discuss.channel.pinned_message_ids`.
- `ir.actions.server.mail_post_method`.
- `res.users.settings`: `push_to_talk_key`, `use_push_to_talk`, `voice_active_duration` and `set_res_users_settings`.

Behaviour: `mail.thread` inherits `mail.track.mixin` and `bus.listener.mixin`; tracking computation moved to `mail.track.mixin` (`_track_finalize` / `_track_execute`); `_action_done` returns only messages; `set_message_pin` is now generic; template rendering returns `partner_cc_ids`.

## How your habits should change

- In templates, use the new `partner_cc` field next to `partner_to`; the composer asks "To" or "Cc" for every recipient.
- Expect "Bookmarked" wording instead of "Starred" in filters and menus.
- Configure one suggested next activity per activity type; old "trigger" chains must be recreated as suggestions, and "Done & Schedule Next" opens the scheduler.
- If you used tracking values in reporting, install the dedicated tracking module.
- Review server actions: "Send Email" is now "Send Message"; use "Log Note" for internal notes.
- Push-to-talk preferences must be set again on each browser/device.
- Persistent call recordings require the cloud storage module; only the artifact framework ships in `mail`.

## What you gain by migrating

- Cc recipients end-to-end, including templates and notification details.
- Native polls in Discuss for quick feedback without extra apps.
- Pinning and clearer read receipts, consistent between Discuss and the chatter.
- A simpler activity flow, with activity plans usable from "Done & Schedule Next".
- Clearer server actions that are less likely to send unintended emails.
- Leaner data: tracking values become optional, and per-device call settings leave the database.
- New tracking and bus-sync mixins that ease future upgrades and customizations.
