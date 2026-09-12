# crm migration guide (14.0 -> 15.0)

Scope: the `crm` app (Community edition). Summary of what changed between Odoo 14.0 and 15.0 for CRM users.

## What's new for users

- **Duplicate detection on contacts**: a stat button lets you spot potential duplicate leads and see all opportunities linked to a contact, including lost ones.
- **Merging keeps more**: when you merge leads/opportunities, related documents (chatter messages, attachments, activities, meetings, and other linked records) are now redirected to the surviving lead, so history is no longer lost.
- **Rule-based lead assignment is now standard**: dispatch and assign leads to teams and salespeople from inside CRM, based on rules and capacities. A salesperson can belong to several sales teams.
- **Predictive Lead Scoring replaces Lead Scoring**: the old Lead Scoring is deprecated; PLS is the supported ranking mechanism, and the legacy scoring step previously tied to automatic assignment is gone.

## Technical data model changes

**crm.lead**
- Added `calendar_event_ids` (One2many to `calendar.event` through `opportunity_id`); `meeting_count` renamed to `calendar_event_count`.
- `merge_opportunity()` now delegates to a private `_merge_opportunity(..., max_length=5)`; the "5 at a time" limit can be relaxed internally (unlimited for superusers).
- Merge logic split into `_merge_dependences_history` (messages + activities), `_merge_dependences_attachments` (renamed "name (from lead)") and `_merge_dependences_calendar_events`.

**res.partner**
- Removed `meeting_ids`, `meeting_count`, `_compute_meeting_count`, `_compute_meeting` and `schedule_meeting()`. The Meetings stat button and kanban pill now come from the Calendar app and work even without CRM installed.

**res.config.settings**
- Removed `crm_alias_prefix` and `generate_lead_from_alias` (the global "Incoming Emails" alias) and `_find_default_lead_alias_id()`. Aliases are configured per sales team.
- Added `crm_use_auto_assignment`, `crm_auto_assignment_action` (Manually / Repeatedly), `crm_auto_assignment_interval_type`, `crm_auto_assignment_interval_number`, `crm_auto_assignment_run_datetime`, plus the `action_crm_assign_leads()` action.

**crm.team**
- Added `assignment_enabled`, `assignment_auto_enabled`, `assignment_max` (sum of member capacities) and `assignment_domain` (value is validated by a constraint).
- New assignment API: `_cron_assign_leads()`, `action_assign_leads(work_days=2)`, `_action_assign_leads()`, `_allocate_leads()`, `_allocate_leads_deduplicate()`, driven by the `crm.ir_cron_crm_lead_assign` cron and the `crm.assignment.bundle` / `crm.assignment.delay` parameters.

**crm.team.member**
- Added `_assign_and_convert_leads(work_days=2)` and `_get_lead_month_domain()`; leads are converted into opportunities during assignment.

## How your habits should change

- Configure incoming email aliases **per sales team** instead of the single global "Incoming Emails" alias in Settings. That option is removed; a global alias can still be created manually from the Aliases menu.
- Meetings are now a Calendar feature: the Meetings button/pill lives there rather than in CRM.
- Set up assignment from CRM Settings: enable Rule-Based Assignment, choose *Manually* (assign from a button on the team form) or *Repeatedly* (scheduled cron), then define each team's Assignment Domain and each member's capacity.
- Use Predictive Lead Scoring instead of the deprecated Lead Scoring.

## What you gain by migrating

- Automatic lead dispatch is now part of the Community CRM app, no longer requiring the Enterprise scoring module.
- Safer merges: messages, attachments, activities and meetings all follow the surviving lead.
- Per-team alias configuration removes the fragile, collision-prone global alias.
- Predictable, capacity-based assignment with automatic duplicate merging during each batch.

The official release notes are fuzzy and mix Enterprise features; only the CRM points above apply to this addon.
