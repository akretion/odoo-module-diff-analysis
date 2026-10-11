# mass_mailing_sms migration guide (19.0 -> 20.0)

## What's new for users

The 20.0 release notes mention one change that directly concerns SMS Marketing: **UTM reference**. UTM handling now follows industry standards. Instead of creating a new UTM Source record every time a mailing (or mass SMS) is created, the mailing itself is stored as the UTM **Reference**, while source, medium and campaign use standardised, reusable values.

In practice, for a contact who converts after clicking a link in a campaign:

- UTM Campaign: Black Friday Sales
- UTM Medium: Email (or SMS for a text campaign)
- UTM Source: Mailing (or Mass SMS)
- UTM Reference: mailing.mailing,42

So SMS mailings automatically carry the medium **SMS** and the source **Mass SMS**, and email mailings use **Email / Mailing**. All of these values are protected records: they can no longer be deleted by accident.

Two side effects on your daily screens:

- The mailing **Name** field is gone. Use **Subject** to tell mailings apart.
- For A/B testing, where several versions share the same subject, a new **A/B Testing Version** field lets you identify each variant.

The other 20.0 release-note items (mailing template library, workflow builder, AI agents, full-screen editing, dynamic lists, etc.) belong to other apps or are Enterprise-only and are not part of this Community addon, so they are not covered here.

## Technical data model changes

- `mailing.mailing` no longer inherits `utm.mixin`; the `utm.mixin` extension of this addon now lives in a dedicated `models/utm_mixin.py` file. Consequence: the `name` field is removed from mailings.
- New computed method `_compute_source_id()` on `mailing.mailing`: an SMS mailing without a source is assigned `mass_mailing_sms.utm_source_mass_sms` ("Mass SMS").
- `_compute_medium_id()` was rewritten: it no longer calls `super()`, and resolves values through `utm.mixin._utm_ref()` using `mass_mailing_sms.utm_medium_sms` ("SMS") and `utm.utm_medium_email` ("Email").
- The SMS medium reference moved from the `utm` module to this addon: `utm.utm_medium_sms` becomes `mass_mailing_sms.utm_medium_sms`.
- `SELF_REQUIRED_UTM_MEDIUMS_REF` is replaced by `SELF_REQUIRED_UTM_REF`, which now covers both media and sources, protecting the "SMS" medium and the "Mass SMS" source from deletion through one generic mechanism.
- The `utm.medium` inheritance (`UtmMedium` model) and its dedicated `ondelete` protection were removed.
- File renames: `models/utm.py` -> `models/utm_campaign.py`; new `models/utm_mixin.py`.
- Sending a mass mailing now runs about 4 additional SQL queries, because the UTM source is computed rather than created together with the mailing record.

## How your habits should change

- Do not look for (or create) one UTM Source per mailing anymore. Use the **Reference** field, which points to the mailing itself, for attribution and reporting.
- Identify mailings by **Subject**; use **A/B Testing Version** when you need to distinguish variants of the same subject.
- Review anything that referenced `mailing.mailing.name`, the old `utm.utm_medium_sms` XML id, or the removed `UtmMedium` deletion guard: filters, favourite views, saved exports, automated actions, Studio-like customisations and integrations need updating.
- If you delete or rename UTM records, note that "SMS" and "Mass SMS" are now system-protected and cannot be removed.
- Historic data is untouched: old mailings keep the source they had; only new mailings use the standardised source, medium and reference.

## What you gain by migrating

- **Reporting you can trust**: UTM campaigns, media and sources now follow common industry conventions, so SMS results line up with email, social and website data instead of polluting your source list with one record per mailing.
- **Cleaner data**: no more automatically generated UTM sources to store, review or clean up, and protected reference values for "SMS" and "Mass SMS".
- **Better traceability**: the UTM Reference points back to the exact mailing, making it easy to see which campaign and which variant produced a sale, a lead or a click.
- **A smoother A/B testing story**: variants are explicitly labelled instead of relying on an ambiguous name field.
- **Lower maintenance**: the addon now relies on the same generic UTM mechanism as the rest of Odoo, which means fewer customisations to carry forward in future upgrades.
