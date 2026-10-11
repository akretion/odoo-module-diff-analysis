# event migration guide (18.0 -> 19.0)

This guide covers the `event` addon (Events app, Community edition) between Odoo 18.0 and 19.0.

## What's new for users

- **Multi-slot events.** An event can now offer several time slots (e.g. a museum exhibition, a cinema screening, a theatre play) and attendees register for a specific slot. Enable it with a new boolean on the event form; a new **Slots** status-bar button opens a calendar view to create slots in bulk.
- **Slot-aware capacity and communications.** For a multi-slot event, maximum attendees, maximum ticket registrations, and scheduled e-mails are computed **per slot**, not for the whole event. Seat and ticket limits still constrain each other.
- **Easier event cancellation.** A new **Cancelled** kanban state lets you cancel an event while keeping it published on the website: registrations are disabled and scheduled communications are stopped (their status shows *Cancelled*).
- **Shared question library.** Questions are no longer duplicated per event or per event template. A question can be shared by several events and templates, and editing it updates all of them (a warning tells you when the question is shared).
- **New communication triggers.** Automated messages can be scheduled "before the event ends" and "after the event starts".
- Event tickets can be sold through the website and sale orders for slot-based events.

## Technical data model changes

**New models**
- `event.slot` – the time slots of an event.
- `event.mail.slot` ("Slot Mail Scheduler") – per-slot communication tracking (`event_slot_id`, `scheduler_id`, `scheduled_date`, `last_registration_id`, `mail_count_done`, `mail_done`).

**New fields**
- `event.event`: `is_multi_slots`, `event_slot_ids` (One2many → `event.slot`), `event_slot_count` (computed). The `seats_max` help now states the limit applies *per slot* for multi-slot events.
- `event.registration`: `event_slot_id` (Many2one → `event.slot`, ondelete restrict, tracked, indexed) and `is_multi_slots` (related).
- `event.mail`: `mail_slot_ids`; `mail_state` gains a `cancelled` value.
- `event.question`: `event_ids`, `event_type_ids` (Many2many) and `event_count` (computed).

**Changed field types and behaviours**
- Questions become Many2many: `event.question_ids`, `general_question_ids`, `specific_question_ids` (relation `event_event_event_question_rel`), `event.type.question_ids`, and `event.question.event_id` / `event_type_id` (now `event_ids` / `event_type_ids`). Default questions are created once (XML ids `event_question_name/email/phone`) and reused; template questions are now linked instead of copied. The constraint forbidding a question linked to both an event and an event type is removed; default questions can no longer be deleted.
- `event.registration.event_begin_date` / `event_end_date` are no longer `related`: they are computed and slot-aware.
- Kanban: `kanban_state` gains `cancel`; `kanban_state_label` and the stage-level `legend_blocked/legend_done/legend_normal` fields are removed. `kanban_state` is now compute-stored and reset to `normal` on stage change.
- `event_registrations_open` is `False` for cancelled events; the scheduler cron skips cancelled events.
- Seat checks now use the new batch helpers `_get_seats_availability()` / `_verify_seats_availability()` on (slot, ticket) tuples. The event-level `_check_seats_availability` constraint is replaced by `_check_slots_dates` (slots must fall inside the event date range) plus an `_onchange_seats_max` warning.

**Method signatures**
- `event.mail._execute_event_based(mail_slot=False)`, `_refresh_mail_count_done(mail_slot=False)`, new `_execute_slot_based()`.
- `event.event._get_date_range_str(start_datetime=False, lang_code=False)`, `_get_ics_file(slot=False)`.
- The registration seat constraint also reacts to `active` and `event_slot_id`.

## How your habits should change

- For multi-slot events, you must first create slots and keep them inside the event's start/end dates, then think "per slot" when setting seat and ticket limits or e-mail timings. Attendees pick a slot before choosing a ticket.
- Lowering `seats_max` below the current number of registrations is now allowed but shows a warning: the event becomes sold out and existing registrations are kept.
- To cancel an event, use the new **Cancelled** state rather than unpublishing or archiving it — the event stays visible with a "Cancelled" banner and pending communications stop.
- Editing a question now affects every event and template using it; duplicate a question if you need event-specific wording. Default questions are protected from deletion.
- Kanban labels: the "Blocked" state is now orange and stage-level label overrides are gone; "Done" is labelled "Ready for Next Stage".

## What you gain by migrating
