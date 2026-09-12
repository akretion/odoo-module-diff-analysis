# mass_mailing migration guide (19.0 -> 20.0)

The official 20.0 release notes contain no extract specific to `mass_mailing`. This guide is therefore built from the code differences between the 19.0 and 20.0 versions of the addon (Community edition).

## What's new for users

**Dynamic Lists replace Favorite Filters.** The single "Favorite Filter" of a mailing is replaced by **Dynamic Lists** (`mailing.filter`, relabelled *Mailing Dynamic List*). Lists are reusable, colourable and archivable, and a mailing can reference **several** of them: its domain becomes the union (OR) of their domains. A list whose recipient model differs from the mailing's is refused.

**Save a domain as a list.** When you build a domain in the domain widget, a *Save as...* button stores it as a new dynamic list and applies it to the current mailing (the mailing must be saved first).

**Recipient count preview.** A live "# Of Recipients" is shown when you select dynamic lists, or set a domain on any model other than `mailing.list` — before you send.

**Richer contact statistics.** Mailing contacts now expose their number of mailings and received / opened / replied / clicked ratios, plus "Last Opened / Clicked / Replied On" dates fed automatically by the trace engine, with drill-down buttons to the related mailings, traces and clicks.

**Multi-mailing statistics.** Opened / Clicked / Replied / Bounced / Delivered buttons now work on several mailings at once; clicks are grouped by email in a graph view. From a dynamic list you can view its recipients, its mailings, or start a new mailing pre-filled with its model and domain.

## Technical data model changes

`mailing.mailing` — removed `mailing_filter_id` (Many2one), `mailing_filter_domain`, `mailing_filter_count` and the methods `_compute_mailing_filter_id`, `_compute_mailing_filter_count`, `action_set_favorite`, `action_remove_favorite`, `action_view_mailing_contacts`, `_action_view_documents_filtered`. Added `mailing_filter_ids` (Many2many through `mail_mass_mailing_filter_rel`), `recipients_count`, new computes (`_compute_mailing_filter_ids`, `_compute_recipients_count`, `_compute_source_id`, `_compute_use_exclusion_list`) and new actions (`action_view_traces`, `action_preview`, `action_import_mailing_contacts`). `mailing_domain` is now the OR of the selected lists; `action_retry_failed` accepts an `extra_domain`.

`mailing.filter` — renamed *Mailing Dynamic List*; new `active`, `color`, `mailing_count`; `mailing_domain` defaults to `[]`; recipient models limited to mailing-enabled ones. New `action_view_recipients`, `action_view_mailings`, `action_send_mailing`, `action_duplicate`.

`mailing.contact` — new `mailing_count`, `received_ratio`, `opened_ratio`, `replied_ratio`, `clicks_ratio`, `last_opened_datetime`, `last_clicked_datetime`, `last_replied_datetime`, and `action_view_mailings/received/opened/replied/clicked`.

`mailing.trace` — `model` and `res_id` are now indexed; `set_bounced`, `set_canceled` and `set_failed` take `failure_reason` and `failure_type`; new `action_retry_failed`.

`mailing.list` — `action_merge` loses its `archive` parameter, `copy_data` is removed, new `action_mailing_lists_merge`.

Elsewhere: `mail.mail` drops `_gc_canceled_mail_mail` and gains `_filter_mail_mail_servers`; `mail.blacklist._track_subtype` becomes `_track_log_get_default_subtype`; new `ir.mail_server._check_owner_user_id_not_mass_mailing`, `res.company.get_mailing_snippet_info` / `update_social_links`, `res.groups._get_light_group_xmlids`, `res.partner._compute_mailing_contact_id` / `action_open_mailing_contacts`, `mail.render.mixin._should_track_node`.

## How your habits should change

- Favorite filters become dynamic lists: you select them in a tag field instead of a single select, and combining several is now normal.
- The inline "Favorite filter domain" field on mailings is gone; manage the domain directly or through reusable lists.
- The Favorite / Remove-Favorite buttons on a mailing disappear: use dynamic lists with their colour and archive flags.
- The `mailing_filter` widget is deprecated; remove it from custom views.
- Statistics buttons aggregate all selected mailings, so rely on trace list / graph filters rather than expecting a single mailing.

## What you gain by migrating

- Reusable, shareable, colour-coded audiences instead of one-off domains.
- Combine several audiences per mailing, with the recipient count verified before sending.
- Per-contact engagement history (ratios and last open/click/reply dates) for sharper segmentation.
- Multi-mailing statistics and one-click navigation from a contact or a dynamic list to its mailings and traces.
