# crm migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes returned no extract matching the `crm` addon, so the points below come from the addon's own change set and cover the Community edition only.

**The Customer field is now always visible on leads.** Previously a lead hid its Customer (partner) field unless a partner was set, the lead was already an opportunity, or you were in debug mode. From 20.0 the partner field is always shown, while the manual company and contact sections (company name, job position, website, address...) are hidden — even in debug mode. The intent is simple: attach a partner instead of typing the same information twice.

**Hidden information stays visible in the chatter.** When a lead has no partner but you filled in some of those now-hidden fields, the creation message in the chatter lists what you entered. All those fields are also tracked, so later changes appear as tracking messages. The same creation summary is logged when leads are created by import.

**Simpler auto-assignment rules.** On a sales team member you now pick one rule instead of juggling two fields:
- Always in rotation (no monthly limit)
- In rotation, with a limit (classic monthly capacity)
- Out of rotation (no new leads assigned)

"Pause assignment" / "Skip auto assignment" is gone — pausing is now simply "Out of rotation" (a capacity of 0). Team forms no longer display the maximum capacity when one of their members is in unlimited rotation, and kanban gauges use an artificial maximum.

## Technical data model changes

`crm.lead`
- Removed `is_partner_visible` (computed boolean).
- Removed `commercial_partner_id` ("Customer Company", computed, non-stored) along with its onchange.
- `function` (tracking 55), `website` (35), `street` (60), `street2` (61), `zip` (62), `city` (63), `state_id` (64), `country_id` (65) are now tracked.
- `_prepare_customer_values(partner_name, parent_id=False)`: the `is_company` argument is gone; a contact under a company now uses `parent_name` instead of `company_name`.
- `_create_customer` and `_handle_partner_assignment` lose `with_parent`; `_message_post_after_hook(message)` loses `msg_vals`; `_track_subtype` is replaced by `_track_log_get_default_subtype`; `_notify_by_email_prepare_rendering_context` gains `force_header` / `force_footer`.
- Lead creation no longer auto-links a lead to a matching company partner by phone/email.

`res.partner` (base, impacts CRM): `company_type` and `company_name` are removed; `is_company` is now computed and stored, and `company_name` is replaced by `parent_name`.

`crm.team.member`: per-member `assignment_optout` is removed and replaced by a non-stored `assignment_rules` selection (Always in rotation / In rotation with a limit / Out of rotation) computed from `assignment_max`. `assignment_max` now defaults to a very large quota (30 000) meaning "no limit"; 0 means out of rotation.

`crm.team`: `assignment_optout` is relabelled "Pause auto assignment"; new `show_assignment_max` field. Actions renamed: `action_open_leads` → `action_open_opportunities`, `action_open_unassigned_leads` → `action_open_unassigned_opportunities`; new `get_team_switcher_data`.

`digest.digest`: `_compute_kpis_actions` replaced by `_get_kpi_custom_settings`.

## How your habits should change

- On a lead, use the Customer field as the single source for company and contact data; the duplicate address/company fields are hidden.
- If you relied on the "Customer Company" field on leads, use the linked partner's company instead.
- Sales managers: re-check each member's rotation rule after migration — the "limited" option resets capacity to 30, and new members default to "Always in rotation".
- Stop writing on removed fields (`company_type`, `company_name`, `commercial_partner_id`, `is_partner_visible`) in imports, templates or automations.

## What you gain by migrating

Cleaner lead capture with one place for customer data, full chatter traceability of company and contact details, simpler and more predictable lead dispatching, and alignment with the simplified partner model where the Person/Company distinction no longer has to be set manually.
