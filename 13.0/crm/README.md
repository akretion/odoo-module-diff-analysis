# crm migration guide (12.0 -> 13.0)
## What's new for users
- **Predictive Lead Scoring.** The win probability is no longer set by hand per stage: Odoo 13 computes it statistically from your own won/lost history (team, stage, tags, and optionally country, state, source, email/phone quality). It shows as "Automated Probability"; you can still override it manually and reset it to automatic in one click.
- **Simplified sales team configuration.** The team form loses the "team type" and the graph-configuration block. One "Manage a pipeline" switch decides whether a team handles opportunities, and any team can be used for any activity without declaring a type.

Note: the release notes also mention Lead Mining, Lead Enrichment, SMS and VoIP call queue. Those are Enterprise/IAP features and are **not** part of this Community addon.

## Technical data model changes
**crm.team**
- Removed: `use_leads`, `dashboard_graph_period_pipeline`, `dashboard_graph_group_pipeline` (plus the `team_type` logic).
- `use_opportunities` kept, relabelled "Manage a pipeline", default `True`.
- Added computed KPIs: `overdue_opportunities_count`, `overdue_opportunities_amount`.
- `opportunities_amount` is now the raw sum of expected revenue: the old probability-weighted ("prorated") calculation is gone.
- Dashboard graph now uses `create_date` / `count(*)` (was expected closing date and `SUM(expected_revenue)`), titled "New Opportunities".
- Alias defaults depend only on the user's "Leads" group, not a per-team flag.

**crm.stage**
- Removed: `probability` (%) and `on_change` ("Change Probability Automatically").
- Added: `is_won` (Is Won Stage?). It is the equivalent of the old `probability = 100`.

**crm.lead**
- `probability` defaults to a fixed 10.0; it is no longer derived from the stage, and changing stage no longer overwrites it (except a won stage, which forces 100).
- Added: `automated_probability` (read-only), `is_automated_probability` (computed), `phone_state`, `email_state` (correct / incorrect / empty, stored).
- New inheritance `phone.validation.mixin`; new DB index on `create_date` + `team_id`.
- New method `action_set_automated_probability()`; `action_set_won()` now finds the stage via `is_won`; stage-based probability code removed from `create`, `write`, `name_create`, `toggle_active` and `_track_subtype`.

**New models**
- `crm.lead.scoring.frequency` (`variable`, `value`, `won_count`, `lost_count`, `team_id`): the statistics table behind scoring.
- `crm.lead.scoring.frequency.field`: whitelist of fields usable for scoring.

**Settings**
- New parameters `crm.pls_start_date` and `crm.pls_fields`, exposed in CRM settings as `predictive_lead_scoring_start_date` and `predictive_lead_scoring_fields`. They are global, not per company.

## How your habits should change
- Stop thinking "stage = percentage". On each stage you now only decide whether it is a won stage; probabilities become an output of your data, not an input.
- On an opportunity the probability is either automatic or manual: typing a value switches it to manual, clicking the shown probability resets it to automatic.
- Sales team forms are shorter: use "Manage a pipeline" plus the "Leads" user group instead of the old type/leads toggles.
- Team dashboard graphs change meaning: newly created opportunities per period, not expected revenue by deadline.
- Keep the daily `crm` scheduled action running: it rebuilds the scoring table and refreshes probabilities.

## What you gain by migrating
- More realistic forecasts: probabilities reflect your actual win/loss history per team and improve as data accumulates.
- A cleaner, less error-prone sales team setup, plus honest KPIs including overdue opportunities.
- Less manual upkeep: no per-stage percentages to maintain.
