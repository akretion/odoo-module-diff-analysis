# survey_crm migration guide (19.0 -> 20.0)

## What's new for users

The official Odoo 20.0 release notes extract provided for this migration does not
contain any section specific to the Survey / CRM bridge (`survey_crm`). This addon
is not covered by the general release notes, so this guide is based on the actual
code changes shipped in the module.

The functional outcome for end users is unchanged: a survey configured with
"Lead Generating" questions still creates an opportunity when a participant
submits an answer, the sales team configured on the survey is still assigned to
the lead, the "Leads" counter is still displayed on the survey form, and the
button that opens those leads still works.

The only visible difference is *how* the link between a survey and the leads it
generated is stored (see below). For most users this is transparent.

## Technical data model changes

This release removes the dedicated "origin survey" fields and replaces them with
the standard UTM reference mechanism.

Removed fields (and the files that declared them):

- `crm.lead.origin_survey_id` (`Many2one` to `survey.survey`) — the file
  `models/crm_lead.py` is deleted.
- `crm.team.origin_survey_ids` (`One2many` to `survey.survey`) — unused, the file
  `models/crm_team.py` is deleted.
- `survey.survey.lead_ids` (`One2many` to `crm.lead`) — the reverse relation is gone.

Changed behaviour:

- The link is now carried by `crm.lead.utm_reference`, whose value is
  `survey.survey,<id>`. Leads created from a survey submission already set this
  value (along with the UTM source `utm.utm_source_survey`); the redundant
  `origin_survey_id` is no longer written.
- `survey.survey._compute_lead_count` no longer depends on `lead_ids`. It performs
  a single grouped read on `crm.lead` filtered on `utm_reference`, instead of one
  grouped read per recordset filtered on `origin_survey_id`.
- The previous guard that returned `0` when the current user had no read access to
  `crm.lead` is removed: the count is now always computed (the read is done in
  superuser mode).
- The action that opens the leads of a survey now uses the domain
  `[('utm_reference', 'in', ['survey.survey,<id>', ...])]` instead of
  `[('origin_survey_id', 'in', ids)]`.

No model or field is added, and no method signature is changed.

## How your habits should change

- If you built custom filters, list views, automated actions or reports on
  `origin_survey_id` (leads) or `lead_ids` (surveys), they will break after the
  migration. Rebuild them on `utm_reference` using the pattern
  `('utm_reference', '=', 'survey.survey,%s' % survey.id)`.
- Do not expect a stored "Survey" field on the opportunity form anymore; use the
  UTM source / medium / campaign and the UTM reference, which is also the
  industry-standard way of tracking lead origin.
- Any custom code writing `origin_survey_id` must be updated to write
  `utm_reference` instead.
- If you used the sales-team field listing surveys, note that it never had an
  effect and is now removed.
- On the survey form, the "Leads" counter now reflects the leads tied to the
  survey through the UTM reference, and is computed even for users without CRM
  access rights.

## What you gain by migrating

- Cleaner, standards-compliant UTM tracking: lead origin is stored once, in the
  UTM reference, instead of a module-specific field, so survey-sourced
  opportunities report consistently with your other campaigns.
- Less data duplication and fewer stored fields to maintain on `crm.lead`,
  `crm.team` and `survey.survey`.
- A single grouped query for the survey lead counter, which scales better when
  you have many surveys and many leads.
- Same functional result as 19.0 for lead generation, sales-team assignment and
  lead drill-down, with one consistent origin concept across the CRM.
