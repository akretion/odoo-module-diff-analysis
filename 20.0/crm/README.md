# crm migration guide (19.0 -> 20.0)

## What's new for users

- **Contact-first lead form.** The company-information and contact-information sections (company name, job position, address, website) are now hidden on leads and opportunities to encourage creating or selecting a real contact. Anything still typed into them is no longer lost: it is summarised in a dedicated creation message in the chatter, and it is now tracked, so later edits also leave a trace.
- **Simpler lead distribution.** Each sales team member gets an auto-assignment rule: *Always in rotation*, *In rotation, with a limit*, or *Out of rotation*. Pausing someone no longer requires a separate checkbox.
- **Pipeline switcher.** You can switch between your teams' sales pipelines from a new dropdown.

*Note:* the "Generate Leads" (Dun & Bradstreet) and "upsell from lead" items of the official 20.0 release notes belong to Enterprise lead mining and subscriptions; they are not part of this Community addon.

## Technical data model changes

**crm.lead**
- `commercial_partner_id` ("Customer Company") removed: it was a UX helper for partner creation, not business logic.
- `create()` no longer promotes `commercial_partner_id` into `partner_id`; lead-to-opportunity conversion now relies on `partner_id` / `partner_name`.
- `is_partner_visible` removed: the Contact field is now always displayed.
- `function`, `website`, `street`, `street2`, `zip`, `city`, `state_id` and `country_id` are now tracked (tracking values 35 to 65).
- New `_has_custom_creation_message()` helper and a `crm.crm_lead_creation_message` QWeb template; the creation message is also logged in batch when leads are imported.
- `_prepare_customer_values(partner_name, is_company=False, parent_id=False)` becomes `_prepare_customer_values(partner_name, parent_id=False)`: the `is_company` argument is dropped and `company_name` is replaced by `parent_name`.
- `_get_customer_information()` no longer sets `is_company` or `parent_id` from `commercial_partner_id`.

**Related base change** (impacts contacts, not only CRM): `company_type` and `company_name` are removed and `is_company` becomes a computed stored field, based on VAT and commercial partner by default, with per-localisation overrides.

**crm.team / crm.team.member**
- `crm.team.assignment_optout` is relabeled "Pause auto assignment"; a new computed `show_assignment_max` hides the team capacity field when a member is unlimited.
- `crm.team.member.assignment_optout` is removed. `assignment_max` default moves from 30 to a very large quota (30 000) meaning "no limit".
- New non-stored, editable computed field `assignment_rules` (`unlimited` / `limited` / `opt-out`) which writes `assignment_max` (30 000 / 30 by default / 0).
- Round-robin assignment now filters members on `assignment_max != 0` instead of the removed opt-out flag.

## How your habits should change

- Prefer the **Contact** field on a lead. Company name, address and job position can still be filled in (debug mode), but they are secondary and no longer drive partner creation.
- The **Customer Company** field is gone; the company behind a lead is deduced from its contact.
- To pause someone's assignment, set their rule to *Out of rotation* (a capacity of 0) instead of ticking "Pause assignment". Existing 19.0 opt-out values are converted by the standard upgrade scripts.
- Keep in mind that edits to the now-tracked address and contact fields appear as messages in the chatter, so users will see more chatter activity.

## What you gain by migrating

- A cleaner, less ambiguous lead form: one customer concept, fewer silent synchronisations between lead fields and the linked partner.
- Full traceability of contact and address information entered on a lead, including through imports.
- Lead dispatch adapted to SME practice: new members are "always in rotation", so leads are assigned immediately, with an explicit limit when you want one, and no separate opt-out flag to maintain.
- Alignment with the 20.0 simplification of contacts in base (`company_type` removed, `is_company` computed), which avoids double maintenance and divergence in your own customisations.
