# maintenance migration guide (16.0 -> 17.0)

## What's new for users

The recurring (preventive) maintenance mechanism has been redesigned. Until now the recurrence was a property of the **equipment** ("Days between each preventive maintenance") and a scheduled action (*ir.cron*) generated the next request. From 17.0 the recurrence lives on the **maintenance request** itself: tick *Recurrent*, then set *Repeat Every* (a number), the unit (*Days / Weeks / Months / Years*) and whether it repeats *Forever* or *Until* an end date. When such a request reaches a stage marked as done, Odoo automatically creates the next one with the recomputed scheduled date. You can therefore have several different rhythms on the same equipment (monthly, yearly…) with no cron job to maintain.

Maintenance data (technician, team, effective date, MTBF/MTTR indicators, maintenance counters) is now shared through a common "Maintenance Maintained Item" mixin, so other maintained items — typically Manufacturing **work centres** — can carry the same maintenance information. Note that the visual blocking of work orders in the Gantt planner is an **Enterprise** MRP feature; the Community `maintenance` addon only provides the maintenance data itself. The "worksheets" also mentioned in the Odoo 17 release notes are an Enterprise feature and are not part of this addon.

## Technical data model changes

**New abstract model `maintenance.mixin`** ("Maintenance Maintained Item") carrying: `company_id`, `effective_date`, `maintenance_team_id`, `technician_user_id`, `maintenance_ids`, `maintenance_count`, `maintenance_open_count`, `expected_mtbf`, `mtbf`, `mttr`, `estimated_next_failure`, `latest_failure_date`. `maintenance.equipment` now inherits it (plus `mail.thread`, `mail.activity.mixin`).

**Removed from `maintenance.equipment`**: `period`, `next_action_date`, `maintenance_duration`, and the methods `_compute_next_maintenance`, `_prepare_maintenance_request_vals`, `_create_new_request`, `_cron_generate_requests`, `_onchange_company_id`. `company_id`, `effective_date`, `technician_user_id`, `maintenance_team_id` moved to the mixin (same field names, so existing data is preserved; the equipment team field is now computed/stored rather than a plain required-by-ondelete field).

**`maintenance.equipment.category`**: new `maintenance_open_count`; `maintenance_count` now includes archived requests (open + archived).

**`maintenance.request`**:
- new recurrence fields: `recurring_maintenance`, `repeat_interval`, `repeat_unit`, `repeat_type`, `repeat_until`;
- `user_id` is now computed (from the equipment technician, otherwise the category technician), stored, still editable;
- `maintenance_team_id` is now computed from the equipment, stored, still editable;
- `company_id` is now required;
- `archive_equipment_request()` also resets `recurring_maintenance`;
- `write()` copies a recurrent preventive request with a new `schedule_date` when it reaches a done stage (respecting the repeat interval/unit and the end date);
- new helpers `_need_new_activity()` and `_get_activity_note()`.

**`maintenance.team`**: counters are now computed with `_read_group` and **exclude archived requests**; `todo_request_count_unscheduled` = total − scheduled.

No method signature change was detected by our comparison tool: the impact is field-level.

## How your habits should change

- Never set the recurrence on the equipment any more: the `period` / `next_action_date` fields are gone. Create a preventive request, tick *Recurrent* and set *Repeat Every* + unit.
- There is no longer a scheduled action to enable for preventive maintenance: the next request is generated automatically when you close the current one.
- Equipment fields *Days between each preventive maintenance* and *Maintenance Duration* disappear; the duration is entered per request.
- Archived requests no longer appear in the team counters, so your dashboard figures may drop — that is expected.
- Technician and team on a request are pre-filled from the equipment but remain freely editable (they are no longer computed on the fly only).

## What you gain by migrating

- **Simpler preventive planning**: one concept (a recurrent request) instead of equipment periods plus a cron job; several schedules per equipment.
- **Fewer lost maintenances**: the next request is created from the done stage, with no dependency on a background job.
- **Consistent equipment and work-centre maintenance** thanks to the shared mixin, preparing cross-app reporting.
- **Cleaner indicators**: MTBF, MTTR and estimated next failure computed from real corrective history.
- **Trustworthy team dashboards**, now that archived requests are excluded from open counts.

