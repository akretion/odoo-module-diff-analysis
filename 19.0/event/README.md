# event migration guide (18.0 -> 19.0)

## What's new for users

- **Multi-slot events.** An event can now be organised in several time slots (movie screening, museum visit, theatre session...). Enable *Is Multi Slots* on the event form, then manage slots from the new **Slots** button and its calendar view. Attendees pick a slot when registering (with or without a ticket). When slots are used, the maximum attendees, the maximum tickets per registration, and every scheduled communication apply **per slot** instead of to the whole event.
- **Easier cancellations.** A new **Cancelled** state is available on the event kanban. A cancelled event can stay published online with a "Cancelled" banner, registrations are blocked, and its pending communications are stopped and shown as *Cancelled*, so no reminder is sent to attendees of a cancelled event.
- **Shared question library.** Registration questions are no longer duplicated per event: one question can be reused by several events and event templates, and translated once. Default questions (Name, Email, Phone) are now shared records and can no longer be deleted; editing a shared question warns you that the change applies to every event using it.
- **New communication triggers.** Two trigger options are added: *Before the event ends* and *After the event starts*.

## Technical data model changes

**Multi-slot.** New model `event.slot` (date, start/end hour, seats, registrations), with constraints on hours, time range and deletion. New `event.mail.slot` model makes each communication schedulable per slot. `event.event` gains `is_multi_slots`, `event_slot_ids`, `event_slot_count`; `seats_max` is applied per slot on multi-slot events. `event.registration` gains `event_slot_id` and `is_multi_slots`; `event_begin_date` / `event_end_date` are no longer related fields but computed from the slot; a slot is mandatory on multi-slot events. Ticket per-order limits are added.

**Cancellation.** `kanban_state` gains the `cancel` value and becomes a computed, stored, editable field; `kanban_state_label` and the `legend_*` fields are removed from `event.event` and `event.stage`. `event.mail.mail_state` gains `cancelled`, and schedulers of cancelled events are skipped.

**Questions.** On `event.question`, `event_id` / `event_type_id` (many2one) become `event_ids` / `event_type_ids` (many2many). Consequently `question_ids`, `general_question_ids`, `specific_question_ids` on events and `question_ids` on event templates become many2many. Default questions now come from XML data. New: `event_count`, `is_reusable`, `action_event_view`.

**Seat checks.** `_check_seats_availability` on events and tickets is replaced by `_get_seats_availability(slot_tickets)` and `_verify_seats_availability(slot_tickets)`. Other signature changes: `_get_date_range_str(start_datetime=False, lang_code=False)`, `_get_ics_file(slot=False)`, `_execute_event_based(mail_slot=False)`, `_refresh_mail_count_done(mail_slot=False)`, `default_get(fields)`, and `write()` no longer resets the kanban state.

**Moved out.** IoT and badge-printing code moved to `event_iot`; OXP badge printer hacks and the 96x134mm badge format were removed; `toggle_active` is deprecated and message-recipient helpers were reworked.

## How your habits should change

- Use the new **Cancelled** state instead of unpublishing or archiving when you want to stop communications.
- For recurring sessions, enable multi-slots and set seats per slot: "Maximum Attendees" now means *per slot*.
- Remember that questions are shared: editing one changes every linked event and template.
- Changing an event's stage now resets its kanban state to *In Progress* (unless it is cancelled), so set the state after the stage.
- If you relied on badge printing or IoT from `event`, install `event_iot` instead.

## What you gain by migrating

You get a genuinely richer Events app: recurring, session-based events are finally supported natively, with per-slot seating, ticketing and communications; cancellations are visible, safe and stop reminders automatically; questions become a reusable, translatable library instead of per-event duplicates; and seat availability is checked consistently across slot/ticket combinations. This means less manual work, fewer duplicate questions, and fewer attendees showing up for a cancelled or finished event.
