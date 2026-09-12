# crm migration guide (13.0 -> 14.0)

## What's new for users

- **Phone and email are always editable on an opportunity.** Even when a customer is set, you can edit them directly on the lead; the change is written to the contact, and editing the contact updates the lead. A ribbon warns you that saving will also update the customer record. Mobile, street, city, etc. stay deal-level values copied from the contact.
- **Search by phone number** on leads/opportunities (at least 3 digits).
- **Lost reasons can no longer be deleted** when used on opportunities (archive them instead), so reporting keeps its history.
- **Easier list work**: UTM fields are available as optional columns, mass editing from CRM list views, and easier "My Activity" view management.
- **Shared tags**: tags are now common to CRM and Sales, visible on sale orders in "Other Info"; the tags of an opportunity are copied to the quotation created from it.

## Technical data model changes

Models and fields:
- `crm.lead.tag` is replaced by `crm.tag`, moved to `sales_team` (shared with Sales); relation table `crm_lead_tag_rel` -> `crm_tag_rel`. `crm.lead.tag` and `crm.lost.reason` were first extracted from `crm_lead.py` into their own files.
- Removed: `partner_address_email` on `crm.lead`. On `utm.campaign`: `crm_lead_activated`, `lead_count`, `opportunity_count` are replaced by `use_leads` and `crm_lead_count`.
- Renamed on `crm.team` (all non-stored, no SQL migration needed): `unassigned_leads_count` -> `lead_unassigned_count`, `overdue_opportunities_count` -> `opportunities_overdue_count`, `overdue_opportunities_amount` -> `opportunities_overdue_amount`; new `lead_all_assigned_month_count`.
- Renamed on `crm.lead`: `planned_revenue` -> `expected_revenue`, `expected_revenue` -> `prorated_revenue` (prorated = expected revenue x probability).

Field behaviour:
- Onchange/defaults became stored editable computed fields (`compute`, `store=True`, `readonly=False`): `team_id`, `stage_id`, `date_open`, `date_last_stage_update`, `contact_name`, `partner_name`, `function`, `title`, `email_from`, `phone`, `mobile`, `website`, `street`, `street2`, `zip`, `city`, `state_id`, `country_id`, `probability`, `automated_probability`.
- `email_from` and `phone` now have an inverse synchronizing them with the customer's email/phone; the new `ribbon_message` field carries the warning.
- Behaviour deltas: at creation the probability now comes from predictive lead scoring instead of 0.0; a team can stay set when the salesperson is cleared in convert/merge wizards.

Methods: `_onchange_partner_id_values` -> `_prepare_values_from_partner`; the old onchange chain (`_onchange_compute_probability`, `_onchange_user_id`, `_onchange_stage_id`, ...) and the `_valid_team` constraint were removed; `date_last_stage_update` is computed on stage change; `crm.team.create/write` keep the mail alias in sync; UTM campaign actions `action_redirect_to_leads` / `action_redirect_to_opportunities` are merged into `action_redirect_to_leads_opportunities`.

## How your habits should change

- Editing a phone or email on an opportunity now updates the contact: check the warning ribbon before saving.
- If you typed in "Expected Revenue", that value is unchanged, but similar-looking columns were renamed (`prorated_revenue`); reports, filters or exports built on the old field names must be reviewed.
- Don't expect the 13.0 salesperson/team coupling: a team can remain without a salesperson, and the default probability of a new lead is the predicted one.
- Tags are shared with Sales: creating or renaming a tag affects both apps.

## What you gain by migrating

- Fewer clicks: contact details are maintained from the opportunity itself, with a clear warning instead of read-only fields.
- Cleaner sales team statistics, including a new "leads/opportunities assigned this month" indicator.
- Field names that match their content (`expected_revenue` vs `prorated_revenue`), making dashboards and exports readable.
- Consistent tags between CRM and Sales, so quotations carry the qualification you did on the opportunity.
- A safer audit trail: lost reasons in use can no longer be deleted.
- Note: the release notes' Outlook integration and recurring revenue (MRR) features are Enterprise-only and are not part of this Community migration.
