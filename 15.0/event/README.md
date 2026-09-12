# event migration guide (14.0 -> 15.0)

## What's new for users

- **Precise registration and ticket sales windows**: ticket “Registration Start/End” and the related event-level sale start are now date **and time** fields. You can open or close sales at a specific hour instead of being forced to midnight.
- **Simpler event templates**: the Event Template form was revamped. The old “Ticketing”, “Use Default Timezone” and “Automatically Send Emails” toggles are gone. You now configure ticket lines, mail schedule lines, timezone, Note and Ticket Instructions directly on the template.
- **Cleaner communications tab**: one **Template** reference field replaces the old “Email Template” field. In Community, only Mail templates are available; the field is designed so other template types can be added by extra modules. The release notes also mention Social Posts scheduling, but that is not part of the Community `event` addon.
- **Better ticket/badge output**: new **Ticket Instructions** on events and event templates. The foldable badge report was reworked and unified, and you can now customise printed tickets/badges more easily from the event form.
- **More reliable scheduler status**: the “Sent” state of automated communications is now tracked with `mail_done`, updated when schedulers run.

## Technical data model changes

- `event.event.ticket`
  - `start_sale_date` (Date) → `start_sale_datetime` (Datetime)
  - `end_sale_date` (Date) → `end_sale_datetime` (Datetime)
- `event.event`
  - `start_sale_date` → `start_sale_datetime`
  - added `event_registrations_started` (computed Boolean)
  - removed `badge_front`, `badge_back`, `badge_innerleft`, `badge_innerright`, `event_logo`
  - added `ticket_instructions` (Html, computed stored readonly=False)
  - `note` is now stored/computed/readonly=False, fed from the event template
- `event.type`
  - removed `use_ticket`, `use_timezone`, `use_mail_schedule`
  - added `note`, `ticket_instructions`
  - `event_type_ticket_ids` and `event_type_mail_ids` are now plain one2many fields, no longer computed through those toggles
- `event.mail.scheduler`
  - removed `mail_sent`, `done`, `template_id`
  - added `mail_done`, `template_model_id`, `template_ref` (Reference)
  - removed `_compute_done`; added `_create_missing_mail_registrations`, `schedule_communications`; `run()` remains as a backward-compatible wrapper
- `event.type.mail`
  - removed `template_id`; added `template_ref`, `template_model_id`, `_prepare_event_mail_values`
- New `mail.template` override filters templates by event context.
- No method signature changes were flagged; field/model changes above are the main migration points.

## How your habits should change

- When configuring sales, choose both a date and a time. Old date-only habits become precise datetime entries.
- On event templates, stop looking for the old toggles: add ticket lines and mail schedule lines directly. Note and Ticket Instructions now flow from template to event.
- Replace badge/logo customisation fields with **Ticket Instructions** on the event or its template.
- In the communication tab, select a **Template** reference. Community event only proposes Mail templates.
- If you have custom code, imports, reports, automations or studio fields, update references from `start_sale_date`, `end_sale_date`, `template_id`, `done` and `mail_sent` to the new field names.
- Scheduler completion is maintained when schedulers execute, not by a stored compute. Keep the scheduled action `run()` / `schedule_communications()` active. Creating or confirming a registration as “open” now triggers after-subscribe schedulers.

## What you gain by migrating

- **Precise control** over when registrations and ticket sales start and end.
- **More predictable email automation**: completion is tracked directly, avoiding costly recomputation on every new registration.
- **Cleaner configuration**: fewer toggles, direct ticket/mail schedule lines, template values copied to events.
- **Better printed output**: custom Ticket Instructions and a reworked foldable badge/ticket report.
- **Future-ready communications**: one template reference field that can support more template types via extra modules.
- A supported, up-to-date Odoo 15 database, with Akretion guiding the field and data migration.
