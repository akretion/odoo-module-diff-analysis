# calendar migration guide (13.0 -> 14.0)

## What's new for users

Of the 14.0 release notes, only one item really concerns the **calendar** addon:

* **Recurrence editing**: when you modify an event that is part of a series, you now choose what to apply the change to: *this event*, *this and following events*, or *all events*.

Calendar views also gain a **year mode**. Everything else in the release notes (Time Off, Planning, generic usability) belongs to other apps, and the Outlook synchronization announced for 14.0 is not part of this Community deployment.

## Technical data model changes

**New model `calendar.recurrence`** ("Event Recurrence Rule") holding the rule itself: `base_event_id`, `calendar_event_ids`, `event_tz`, `rrule` (stored, computed + inverse), `dtstart`, `rrule_type`, `end_type`, `interval`, `count`, `mo…su`, `month_by`, `day`, `weekday`, `byday`, `until` and a computed `name`.

**`calendar.event`**
* Added: `recurrence_id` (Many2one to `calendar.recurrence`), `weekday`, `until`, and the transient helper `recurrence_update` (`self_only` / `future_events` / `all_events`).
* All recurrence settings (`rrule`, `rrule_type`, `event_tz`, `end_type`, `interval`, `count`, `mo…su`, `month_by`, `day`, `byday`) are now computed/invertible proxies of `recurrence_id` instead of stored columns on the event.
* Removed: `recurrent_id`, `recurrent_id_date`, `week_list`, `final_date` (replaced by `until` on the recurrence), and the "virtual id" helpers (`calendar_id2real_id`, `get_real_ids`, `real_id2calendar_id`, `any_id2key`, `is_calendar_id`).
* `duration` is now computed with an inverse (writing the duration recomputes `stop`); default order changed from `id desc` to `start desc`.
* `end_type` gains a **`forever`** option; one recurrence generates at most **720 events** (`MAX_RECURRENT_EVENT`).
* Removed methods: `get_recurrent_ids`, `_get_recurrent_date(s)_by_event`, the event-level `_rrule_serialize` / `_rrule_parse`, `_get_recurrency_end_date`, `detach_recurring_event`, `action_detach_recurring_event`, and the `read` / `_read` / `_search` / `copy` / `export_data` overrides. New methods: `_apply_recurrence_values`, `_split_recurrence`, `_break_recurrence`.
* Signature changes: `unlink(can_be_deleted=True)` → `unlink()`; `_send_mail_to_attendees(..., force_event_id=)` → `(..., ignore_recurrence=)`; `calendar.alarm_manager._get_next_potential_limit_alarm(..., partner_id=)` → `(..., partners=)`, plus a new `_get_partner_next_mail(partners=None)`.

**`calendar.attendee`**: `event_id` and `partner_id` are now **required** (cascade delete on `event_id`); `email` becomes a related field of `partner_id.email`; new related `recurrence_id`.

**Removed inherited models**: the `ir.attachment` and `mail.message` overrides — only needed to translate virtual ids — are gone.

## How your habits should change

* Recurring events are now **materialized**: each occurrence is a real `calendar.event` record. No more virtual ids in URLs, searches, chatter or attachments.
* A series is capped at **720 occurrences** (roughly 2 years daily, 15 years weekly). "Forever" means 720 events; re-edit the series later to generate more.
* Changing the rule, or drag & dropping an occurrence, **rebuilds** the series. Only occurrences strictly at the same date/time are kept: renaming or re-inviting one occurrence can be lost, as in Google Calendar.
* "Modify all events" is refused when dates or times change — use *this and following events* instead.
* Attendee lists are derived from the event's attendees: adding or removing a partner updates invitations automatically, and attendees follow the event.

## What you gain by migrating

* One consistent, predictable behaviour for recurring events, aligned with Google Calendar / Outlook habits.
* Occurrences are plain records: searchable, reportable, filterable, with correct chatter, attachments and reminders.
* No more "virtual id" workarounds, and no unbounded recurrence computation that can freeze large calendars.
* A clean `calendar.recurrence` model that future customizations and integrations can build on.
* Deliverable on the Community edition: the recurrence rework is core `calendar`, no Enterprise addon required.
