# mass_mailing migration guide (13.0 -> 14.0)

## What's new for users

The Odoo 14.0 release notes contain **no dedicated mass_mailing feature**. This addon is not covered by the official highlights: the work done on it in 14.0 is an internal refactoring of how mailing options are computed. Functionally, a mailing is still created, tested and sent exactly as before.

The generic 14.0 usability improvements you *will* notice while working with Mailing Lists, Mailings and UTM Campaigns (all part of Community) include:

- New listview design, with record counts in the search panel, "select all records" beyond the first page, and records automatically unselected after a mass edition.
- Colored tags in UTM listviews, new date filters across apps, drill-down from bar/pie charts, and sample data on empty reporting views.

## Technical data model changes

Changes come from the source patches. No model was added or removed, and no method signature changed.

**mailing.mailing**
- `medium_id`: now a stored, editable computed field (`_compute_medium_id`, depends on mailing_type). It sets the Email UTM medium by default; the forced value previously written in `create()` was removed.
- `reply_to_mode`: was a required field filled by `default_get()`; now stored, editable computed (`_compute_reply_to_mode`, depends on mailing_model_real). Still 'email' for res.partner / mailing.contact, 'thread' otherwise.
- `reply_to`: now stored, editable computed (`_compute_reply_to`, depends on reply_to_mode). Filled with the current user's email in 'email' mode, cleared in 'thread' mode.
- `mailing_domain`: now stored, editable computed (`_compute_mailing_domain`, depends on mailing_model_name and contact_list_ids). The old onchange logic (blacklist / opt-out / list filter) is kept but moved into the compute.
- `mailing_model_real`: compute only, no default and no longer required. `mailing_model_id` is now required with `ondelete='cascade'`, defaulting to Mailing List.
- Removed: `default_get()`, `_onchange_model_and_list()`, `_onchange_mailing_type()`.

**utm.campaign**
- Removed unused computed statistics and their compute methods (`_compute_mailing_items`, `_compute_mailing_clicks_ratio`): mailing_clicks_ratio, mailing_items, mailing_clicked, total, scheduled, failed, ignored, sent, delivered, opened, replied, bounced.
- Kept: received_ratio, opened_ratio, replied_ratio, bounced_ratio, now computed per campaign and explicitly set to 0 when a campaign has no trace.

## How your habits should change

- Mailing options are now stored: recipient model, reply-to mode, reply-to address, UTM medium and recipient domain are computed for you but stay editable, and your input is kept on save. This removes the old onchange/default_get behaviour where the value could be recomputed unexpectedly.
- Changing the recipient model or the selected contact lists re-computes those fields, so review the Reply-To and Domain after switching recipients.
- Setting Reply-To mode to "Recipient Followers" clears the Reply-To address; this is expected.
- Since the logic now lives in compute fields triggered on create and write as well, mailings created by imports or automated actions behave like mailings created by hand in the form.
- On Campaign forms, some raw counters (total, sent, delivered, opened, ...) disappear. Keep using the mailing's own statistics and the ratio fields displayed on the campaign.

## What you gain by migrating

- Predictable behaviour in every flow: user interface, imports, automated actions and integrations now produce identical values, with no hidden onchange.
- Fewer dead fields and dead code on UTM Campaigns, and statistics that reliably show 0 instead of leftover values when a campaign has no trace.
- A cleaner technical base, making future customisations of your mailing and campaign flows easier and safer.
- Plus everything the 14.0 interface brings (new listviews, mass edition, select all, search panel counts, UTM colored tags) and a maintained, supported version instead of Odoo 13.
