# maintenance migration guide (19.0 -> 20.0)

_No official 20.0 release note covers this addon: everything below comes from the code diff of the Community `maintenance` module._

## What's new for users

- **Quick state actions on requests.** A new state widget (same idea as the one on project tasks) lets you mark a request as **Done** or **Cancel** directly from the kanban card or the form, instead of dragging it to a "Done" stage or opening the form to press Cancel.
- **Clearer request states.** The old kanban state (In Progress / Blocked / Ready for next stage) and the separate "Done" stage flag are replaced by one visible state: In Progress, Changes Requested, Approved, Done, Cancelled.
- **Several technicians per request.** The single "Technician" field becomes "Technicians" (many). Avatars are shown on the kanban card and can be edited straight from it.
- **Simpler Instructions tab.** Only the rich-text instructions remain; the PDF and Google Slide options are gone — attach or link any document inside the instructions text.

## Technical data model changes

`maintenance.stage`
- `done` (Boolean "Request Done") **removed**.

`maintenance.request`
- `kanban_state` **removed**; new `state` selection: `normal`, `changes_requested`, `approved`, `done`, `cancelled` (required, default `normal`, tracked, not copied).
- `done` field (related to `stage_id.done`) **removed**.
- `user_id` (Many2one "Technician") → **`user_ids`** (Many2many "Technicians"), still computed, stored and tracked; form, kanban, list, calendar and activity views updated.
- `instruction_type`, `instruction_pdf`, `instruction_google_slide` **removed**; `instruction_text` kept.
- `archive_equipment_request()` → **`cancel_equipment_request()`** (sets the state to Cancelled); `reset_equipment_request()` puts the request back to In Progress.
- `close_date` is set when the state becomes Done and cleared on other state changes; changing the stage resets Changes Requested/Approved to In Progress.
- Team counters: `todo_request_count_block` → **`todo_request_count_changes_requested`**; open/closed counts on teams and categories now use `state` instead of `stage_id.done` and `archive`.
- Follower logic refactored (`_add_new_followers`): only new assignees are subscribed, so a manually unsubscribed user is not re-added.
- Internal renames: `_track_subtype` → `_track_log_get_default_subtype`; `activity_update`, `_get_activity_note` and `_need_new_activity` removed; `_compute_stage_id` and `_read_group_equipment_id` added.

`maintenance.equipment`
- New computed assignment helpers (`_compute_is_assigned`, `_search_is_assigned`, `_compute_owner`, `_compute_equipment_assignment_fields`, `_get_assign_fields` and handler resolvers) back a new "is assigned" / "owner" notion on equipment.

## How your habits should change

- Stop moving requests to a "Done" stage: use the state button (Done / Cancel) on the card or form.
- "Blocked" no longer exists. Use Changes Requested or Approved; the team counter is now "Number of Requests with Changes Requested".
- Administrators: the "Request Done" checkbox is gone from maintenance stages — a stage no longer decides whether a request is finished. Review stage configuration and automations relying on it.
- Instructions: paste URLs or upload documents inside the text instead of the removed PDF/Google Slide fields.
- Assignment: you can assign several technicians at once, and you can unsubscribe yourself from a request without being re-added later.
- Existing data must be converted (old kanban state → state, stage "done" → Done state, technician → technicians). Check custom filters, reports, dashboards and automated actions referencing `done`, `kanban_state`, `user_id` or the `instruction_*` fields before going live.

## What you gain by migrating

- Faster everyday handling: two clicks to close or cancel a request, from the kanban or the form.
- Better team work: several technicians per request, visible and editable on the card.
- Cleaner, more reliable reporting: a single state field drives open counts, close dates and team counters.
- Less clutter: one instructions field to maintain instead of three.
