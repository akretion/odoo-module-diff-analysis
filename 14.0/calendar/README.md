# calendar migration guide (13.0 -> 14.0)

## What's new for users

Recurring events were rebuilt in 14.0. The release notes summarise the visible part: you can now "modify a particular event, the selected event, and all future events, or every event that is part of a series". Concretely, editing an event of a series asks whether to apply the change to this event only, to this and following events, or to all events. The same choice appears when you change the repetition rule itself ("all events" is refused when dates or times change, as in Google Calendar), and a new termination type, Forever, keeps a series running without an end date.

Occurrences are no longer virtual: each one is a normal event record, so it can be found in list views, searches, groups and reports. The old restriction "group by date is not supported" is gone.

The calendar view also gains the new 'year' mode announced in the release notes.

Note: the Outlook calendar synchronization announced for 14.0 is not part of this Community module.

## Technical data model changes

New model: `calendar.recurrence` ("Event Recurrence Rule"), holding `rrule`, `dtstart`, `rrule_type`, `end_type` (new `forever` value), `interval`, `count`, weekday flags (`mo`…`su`), `month_by`, `day`, `weekday`, `byday`, `until`, `event_tz`, `base_event_id` and `calendar_event_ids`.

`calendar.event`
- Added: `recurrence_id` (Many2one), `recurrence_update` (non-stored selection: self_only / future_events / all_events), `until`.
- Recurrence settings (`rrule`, `rrule_type`, `event_tz`, `end_type`, `interval`, `count`, weekday flags, `month_by`, `day`, `byday`) become non-stored computed fields (readonly=False) proxying `recurrence_id`.
- Removed: `recurrent_id`, `recurrent_id_date`, `week_list`, `final_date`, `display_start`, `start_datetime`, `stop_datetime`.
- Changed: `_order` is now `start desc`; `start` is tracked; `stop` is computed/stored, tracked, defaults to today; `duration` is computed/stored and writable (writing the duration or moving the start recomputes `stop`).

`calendar.attendee`: `event_id` and `partner_id` are now required; `email` is related to `partner_id.email` and no longer editable; new related `recurrence_id`; partners are subscribed/unsubscribed automatically; inviting yourself sets the state to accepted.

Misc: the module no longer inherits `ir.attachment` and `mail.message`.

API changes: `_get_next_potential_limit_alarm(..., partner_id=)` → `partners=`; `_send_mail_to_attendees(..., force_event_id=)` → `ignore_recurrence=`; `create_attendees()` replaced by `_attendees_values()`; removed `get_recurrent_ids()`, `get_search_fields()`, `_get_recurrent_dates_by_event()`, `_get_recurrence_end_date()`, `_compute_display_start()`, `_inverse_duration()` and the duration/date onchange handlers; `_get_recurrent_fields()` now returns a set.

## How your habits should change

- Expect a scope question every time you edit a series; "all events" is refused for date/time changes.
- Changing a repetition rule regenerates the series: only occurrences falling at exactly the same time survive, so per-occurrence customisations (subject, attendees, chatter) may be lost. Dragging a recurring event in the calendar does the same: the rule is updated and manual outliers disappear.
- A series never creates more than 720 occurrences (about 2 years for a daily recurrence, 15 years weekly); "Forever" is a shortcut for those 720. Very long series must be extended as time passes.
- Occurrences are real records, so they appear in list views, searches, groups and reports — handy, but mass edits can hit every occurrence.
- After migration, review your recurring events and test reminders and invitations, which were refactored.

## What you gain by migrating

- A modern, maintainable recurrence engine: recurring events now behave predictably everywhere (list, search, group by, reporting) instead of existing only inside the calendar view.
- Google-Calendar-like editing options and the new Forever end type.
- Cleaner attendee handling: emails stay in sync with contacts, followers follow attendees, self-invitations are auto-accepted.
- Removed dead fields (`start_datetime`, `stop_datetime`, `display_start`) simplify imports, integrations and custom reports built on the calendar models.
- The 14.0 calendar view improvements, including the year mode.
