# event_crm migration guide (13.0 -> 14.0)

`event_crm` is a **new addon introduced in Odoo 14.0**. It simply does not exist in 13.0, so migrating is not a rewrite of an existing feature: it means installing a new bridge between Events and CRM and configuring it.

## What's new for users

The official 14.0 release notes cover this addon under Events > **CRM Integration**:

- Define rules to create Leads/Opportunities from attendees when they register.
- Leads can also be created when an attendee is either created, confirmed, or marked as having attended.

Concretely, inside an event you get a new **Lead Generation** configuration tab where you define rules:

- **When** the rule fires: attendee created, attendee confirmed, or attendee attended. Lead quality increases with confirmation and attendance, so you can choose the moment that matters for you.
- **How many leads**: one lead per attendee (B2C), or one lead per group / per order (B2B). The per-order option is only selectable in the interface when attendees can be registered as a group in a single action (for example with `website_event` or `event_sale` installed).
- **Which attendees**: filter by event, by event category, by company, or with a custom registration filter (a search domain).
- **What the lead looks like**: lead type (Lead or Opportunity), salesperson, sales team and tags are pre-filled automatically. Contact details (customer, name, email, phone, mobile, contact name) are taken from the registration.
- Event, category and company conditions combine: if both an event and a category are set, matching either one is enough.

Several rules can match the same attendee on purpose, so more than one lead can be generated if that is what you configured.

Stat buttons link both worlds: on the event you see how many leads were generated, on a lead you see its source registrations, and on a registration you see the leads it produced.

## Technical data model changes

New model **`event.lead.rule`** (Event Lead Rules) with: `name`, `active`, `lead_ids`, `lead_creation_basis` (attendee / order), `lead_creation_trigger` (create / confirm / done), `event_type_ids`, `event_id`, `company_id`, `event_registration_filter`, `lead_type` (lead / opportunity), `lead_sales_team_id`, `lead_user_id`, `lead_tag_ids`.

Inherited fields added:

- **`crm.lead`**: `event_lead_rule_id`, `event_id`, `registration_ids`, `registration_count`.
- **`event.event`**: `lead_ids`, `lead_count` (counter computed per event, visible to salespeople).
- **`event.registration`**: `lead_ids` (read-only, not copied), `lead_count` (visible to salespeople).

Automation hooks: `event.registration` `create()` and `write()` run the matching rules. When a registration already produced a lead, the existing lead is **updated** rather than duplicated — contact fields and the description are refreshed when the partner or the registration contact details change. For group-based rules, only a partner change triggers a refresh, the rest stays available through the stat button.

No existing method signatures were changed; the addon only adds new models, new fields and new hooks.

## How your habits should change

In 13.0, generating leads from an event was entirely manual: someone had to look at the attendee list in Events and create the lead or opportunity by hand in CRM, then retype the contact details. Nothing connected the two records afterwards.

In 14.0 you configure the rules once, per event or globally, and the leads appear by themselves as attendees are created, confirmed or marked as attended. There is no more copy-pasting of contacts, and no more forgetting a follow-up.

Two habits to keep in mind:

- Define your rules **before** the registrations start flowing; a rule triggers on future attendee events, so late configuration does not retroactively create leads.
- Check the trigger level you pick. Choosing "attendees are created" will generate more, noisier leads than "attendees attended".

## What you gain by migrating

- **Events become a real lead source**, automatically feeding CRM instead of being a dead-end attendee list.
- **No manual data entry**: contact details, event reference, UTM campaign/source/medium, salesperson, team and tags are filled in for you.
- **Consistent qualification**: the same rules apply to every event, so lead quality no longer depends on who reviews the attendee list.
- **Full traceability**: every lead knows the rule and the registrations that created it, and every registration knows which leads it produced, navigable with one click.
- **Flexible B2C and B2B handling**: one lead per attendee for consumers, one lead per order for group registrations.

