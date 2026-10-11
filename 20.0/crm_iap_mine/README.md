# crm_iap_mine migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes shipped with this migration cover Manufacturing and Accounting only. **They contain no announcement about Lead Generation / CRM lead mining.** Nothing in that extract applies to this addon, so no feature described there should be expected in your Community database after the upgrade — most of it concerns other apps, and several of the listed items are Enterprise-only.

The only functional change affecting `crm_iap_mine` is a removal: the **"people" target (Companies and their Contacts)** disappears from Lead Generation requests. Since the provider moved from Clearbit to Dun & Bradstreet, direct contact data (GDPR-restricted) can no longer be returned, so Odoo stops offering that option here and keeps contact-oriented enrichment in the separate `website_crm_iap_reveal` app (Clearbit usage). In practice, 20.0 lead mining requests generate **companies only**.

## Technical data model changes

Removed fields on `crm.iap.lead.mining.request` (9 deletions, no additions):

- `search_type` (Target: Companies / Companies and their Contacts)
- `contact_number` (Number of Contacts)
- `contact_filter_type` (role / seniority)
- `preferred_role_id`, `role_ids`, `seniority_id`
- `lead_contacts_credits`, `lead_total_credits` (computed tooltips)

Related behaviour changes:

- `lead_credits` tooltip now depends only on `lead_number`; the contact-credit and total-credit tooltips are gone.
- The IAP payload always sends `'search_type': 'companies'`; `contact_number`, `contact_filter_type`, `preferred_role`, `other_roles` and `seniority` are no longer transmitted.
- The `MAX_CONTACT = 5` constant and the `_onchange_contact_number` handler are removed.
- Helper signature changed: `crm.iap.lead.helpers.lead_vals_from_response(lead_type, team_id, tag_ids, user_id, company_data)` — the `people_data` argument was dropped, along with the block that used to prefill `contact_name`, `email_from` and `function` on the lead.
- `crm.iap.lead.mining.request._lead_vals_from_response` no longer builds `people_data`.
- The `crm.iap.lead.role` and `crm.iap.lead.seniority` models themselves are not deleted by this change; only the request fields pointing to them.

Because fields are removed, stored values (number of contacts, preferred role, seniority, filter type) on existing requests are discarded during the upgrade. Odoo provides an upgrade script (odoo/upgrade#9650) to carry the database through this change.

## How your habits should change

- You can no longer ask for contacts as part of a lead mining request. "Companies and their Contacts" is not an option anymore.
- Requests previously configured with the people target simply become company requests after migration; the extra filter fields disappear from the request form.
- Credit estimation is simpler: 1 credit per company requested. The old "additional credits for contacts" and "total credits" indications are no longer displayed, so don't plan budgets around them.
- If you need contact-level enrichment, use the dedicated reveal flow (`website_crm_iap_reveal`, Clearbit-based) rather than lead mining — and check your subscription/provider availability, as that capability is no longer wired into this addon.
- Any automation, studio field, or export referencing the removed fields must be updated before upgrading.

## What you gain by migrating

- A **cleaner, less misleading request form**: one target (companies), one credit rule, no options the provider cannot fulfil.
- **GDPR-safe behaviour**: Odoo no longer promises contact data that Dun & Bradstreet cannot legally deliver, reducing false expectations and blocked requests.
- **Simpler maintenance**: fewer fields, one payload shape and a simplified helper API, which reduces custom-code breakage over time.
- A supported upgrade route with an official migration script.

Honest caveat: if your 19.0 process depended on importing contacts through lead mining, 20.0 does not replace that here — plan the reveal app instead. For strictly company-based prospecting, the experience is unchanged apart from the streamlined form.
