# calendar migration guide (19.0 -> 20.0)

## What's new for users

- **Multiple calendars in one place.** You now create and manage several calendars per user — including shared team calendars — instead of one implicit calendar per user.
- **View other people's calendars.** The "Meet with" sidebar feature lets you pick colleagues, overlay their calendars, then create an event that automatically adds them as attendees.
- **Manage pending activities.** Calendar events now carry activities (standard activity scheduling and status tracking), so meetings can be followed up from the Calendar app.
- **Friendlier sidebar filters.** Attendee filtering moves from a list of checkboxes to a searchable attendee/tag selector with colours, plus a "My calendar" on/off switch. The filters you keep selected become the default attendees of new events.

The release notes also list Booking Pages, mobile redesign, meeting-title time detection and Google/Outlook sync. Those belong to the Appointments app, to front-end work, or to the separate Google/Outlook connector modules — not to this base `calendar` addon — so they are not covered here.

## Technical data model changes

The user-to-event relation is now indirect:

`res.users --o2m--> calendar.user --m2o--> calendar.calendar --o2m--> calendar.event`

- **`calendar.calendar` (new model)**: `name`, `event_ids`, `recurrence_ids`, `calendar_user_ids`, `owner_id`, `share_user_ids`, `calendar_user_id`, `color`, `is_primary`, `user_access_role`, and `calendar_default_privacy` (moved from `res.users`; values public / private / confidential / members_only).
- **`calendar.user` (new model)**: the membership record — `calendar_id`, `user_id`, `name`, `is_primary`, `access_role` (Owner / Write), and filter fields `filter_color`, `is_filter_active`, `is_filter_checked`. Unique index on (user, calendar); only one primary calendar per user.
- **`res.users`**: `calendar_default_privacy` removed; added `calendar_user_ids`, `calendar_ids`, `owned_calendar_ids`, `writable_calendar_ids`, `primary_calendar_id`. A migration / post-init hook creates one undeletable primary calendar per existing user; new users get one on creation.
- **`calendar.event`**: added stored `calendar_id` (cascade delete) and `calendar_color`; `privacy` and `effective_privacy` gained the `members_only` value; `effective_privacy` is now searchable and falls back event → calendar → organiser's primary calendar → public. `activity_ids` was renamed **`meeting_activity_ids`**, and the model now also inherits `mail.activity.mixin` (standard `activity_ids`). `_check_calendar_privacy_write_permissions()` was removed: privacy rules now derive from `effective_privacy` and the calendar access role. New `_before_calendar_cascade_unlink()` hook on events and recurrences.
- **`calendar.recurrence`**: new related `calendar_id`; `base_event_id` is now indexed.
- **`calendar.filters`**: `partner_checked` removed (`active` now means "filter enabled for this user"); `init_partner_filters()` / `update_partner_filters()` added, `unlink_from_partner_id()` removed; inactive filters are purged after a month.
- **`res.users.settings`**: new `calendar_hide_my` option.

## How your habits should change

- Default privacy is no longer a user preference: set it **per calendar**. Events without explicit privacy inherit their calendar's default.
- Manage calendars from Configuration or from your preferences; share one with colleagues and grant Owner or Write rights through "Shared with".
- Your primary calendar cannot be deleted. Deleting any calendar also deletes its events and recurrences.
- Attendee filters are no longer remembered as a checkbox list: the selection is rebuilt from context, inactive filters are purged after a month, and "My calendar" controls whether your own events show.
- Meetings created from an activity now default to the activity's user plus the linked record's customer as attendees.
- Customisations referring to `activity_ids` on events, `calendar_default_privacy` on users, `partner_checked`, or `_check_calendar_privacy_write_permissions` must be updated.

## What you gain by migrating

- **Team calendars**, impossible in 19.0: several users share and co-maintain a calendar with per-calendar privacy, colour and ownership.
- **Better day-to-day planning**: overlay colleagues' calendars via "Meet with", pick attendees with a modern selector, and track activities directly on events.
- **A safe upgrade path**: the automated migration creates each user's primary calendar and reattaches existing events, so current working habits keep functioning.
