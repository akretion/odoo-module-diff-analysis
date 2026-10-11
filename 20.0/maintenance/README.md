# maintenance migration guide (19.0 -> 20.0)

## What's new for users

The 20.0 cycle reworks how a maintenance request lives its life: how it is closed, who is on it, and what instructions it carries.

- **New Status widget.** Requests can be marked **Done** or **Cancelled** in one click from the kanban card or the form, instead of moving the card to a "Done" stage, or opening the form to press Cancel. The interaction mirrors project tasks.
- **Simpler Instructions tab.** The PDF and Google Slide options are gone. A single rich-text field remains, where you write instructions and attach or link any document.
- **Multiple technicians.** A request can now be assigned to several technicians. Kanban cards show them with the standard avatar widget, and the status icon and avatars were swapped to match standard Odoo layouts.

The official release notes for Maintenance also mention dedicated Gantt views for maintenance requests and per-team maintenance stages. Gantt views are an Enterprise feature, and per-team stages are not part of the data changes below, so neither is included in a Community migration. The remaining release-note entries that touch maintenance (Field Service equipment, Manufacturing work centers, industry packages) belong to other apps.

## Technical data model changes

**maintenance.request**
- Added `state` (Selection, required, default `normal`, tracked, not copied): `normal`, `changes_requested`, `approved`, `done`, `cancelled`. It replaces the old `kanban_state` (`normal` / `blocked` / `done`); "blocked" becomes "changes requested".
- Removed the `done` boolean (it was related to `stage_id.done`).
- `user_id` (Many2one, "Technician") **replaced by** `user_ids` (Many2many, "Technicians"), computed by the new `_compute_user_ids` from the equipment/category technician, filtered by company.
- Removed `instruction_type`, `instruction_pdf` and `instruction_google_slide`; `instruction_text` remains.
- `archive_equipment_request()` renamed to `cancel_equipment_request()`: it now sets `state = cancelled` (and stops recurrence) instead of archiving.
- `reset_equipment_request()` puts the request back in the first stage and resets its state to `normal`; it no longer toggles the archive flag.
- `write()` no longer resets the kanban state when the stage changes; it clears `close_date` when leaving `done` and sets it when entering `done`.
- New `_add_new_followers()`: only newly added owner/technicians are subscribed, so a user who unsubscribed manually is not re-subscribed.

**maintenance.stage**
- The `done` boolean is removed: a stage is no longer "done". Done-ness comes from the request state.

**maintenance.team**
- `todo_request_count_block` renamed to `todo_request_count_changes_requested`.
- Counters now rely on `state`: open requests are `normal`, `changes_requested` and `approved`; `done` and `cancelled` count as closed. `archive` no longer drives the counters.

**maintenance.mixin** (equipment, categories)
- MTTR/MTBF and the request counters now depend on `state` instead of `stage_id.done`/`archive`.

## How your habits should change

- **Stages:** there is no "Request Done" checkbox anymore, so nothing to configure there; the state of each request decides what is finished.
- **Closing a request:** use the status button (Done / Cancel) from the kanban or the form.
- **Cancel is not archive:** cancelled requests stay in the pipe as *Cancelled*. Keep archive for hiding records.
- **Blocked is gone:** use *Changes Requested* to flag a request, typically before sending it back.
- **Assignments:** pick as many technicians as needed; only the ones you add become followers.
- **Instructions:** paste links and add attachments inside the text field; the dedicated PDF/Google Slide inputs no longer exist.
- **Filters, reports and custom code** built on stage "done", on archive, or on the single Technician field must be rebuilt on State and Technicians.

## What you gain by migrating

- **Fewer clicks, faster handling:** finish or cancel a request straight from the kanban card.
- **Explicit statuses:** changes requested, approved, done, cancelled — no more overloading stages and the archive flag to express "finished".
- **Real teamwork:** several technicians per request, with avatars on the card and correct follower handling.
- **A cleaner instructions tab:** one field for text, links and attachments.
- **Consistency and reporting:** the same UX as project tasks, and per-team / per-equipment counters that follow the actual request state.
