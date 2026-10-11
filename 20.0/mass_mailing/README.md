# mass_mailing migration guide (19.0 -> 20.0)

## What's new for users

**Dynamic mailing lists** — Instead of rebuilding a recipient domain for every campaign, you can now create reusable dynamic lists: a recipients model plus a set of rules (domain). When composing a mailing you simply pick one or more dynamic lists; recipients are computed when the mailing is processed. Ready-made list templates, a "save as..." button in the domain editor and a live total recipient count make lists easy to create, reuse and check.

**Mailing template library** — Any mailing can now be saved as a real template (cog menu) and reused from a dedicated **Templates** menu. Templates are true mailing records: they can be edited, duplicated and archived. Templates do not require a "Send From" address, and only email mailings can be saved as templates.

Other Email Marketing / Marketing Automation novelties in the 20.0 release notes (conditional content, favorite blocks, full-screen design editing, new fonts, snippets, social media accounts, AI campaign creation, workflow builder, webhooks) belong to Enterprise-only features or to other apps and are **not** part of this Community addon.

## Technical data model changes

**mailing.filter**
- Model description changed from "Mailing Favorite Filter" to "Mailing Dynamic List".
- Added `active`, `color`, `mailing_count`; `name` is now "List Name"; `mailing_domain` defaults to `[]`.
- Recipients model is restricted to mailing-enabled models.
- New actions: view recipients, view mailings, send mailing, duplicate.

**mailing.mailing**
- Removed: `mailing_filter_id` (Many2one), `mailing_filter_domain`, `mailing_filter_count`, `favorite`, `favorite_date`.
- Added: `mailing_filter_ids` (Many2many, computed/stored, "Dynamic Lists"), `is_template` (Boolean, tracked), `recipients_count` (computed).
- `mailing_domain` is now the OR-union of all selected dynamic list domains.
- New constraints: every dynamic list must target the same recipients model as the mailing; templates are email-only.
- `email_from` is still mandatory for non-template mailings.
- `action_fetch_favorites` replaced by `action_fetch_templates`; new `action_save_as_template` and `action_new_mailing_from_template`.
- Statistics actions (clicked, opened, replied, bounced, delivered) now support several mailings at once (`in self.ids`).

**mailing.contact**
- Added `mailing_count`, `received_ratio`, `opened_ratio`, `replied_ratio`, `clicks_ratio`, plus `last_opened_datetime`, `last_clicked_datetime`, `last_replied_datetime` (kept in sync from mailing traces).
- New actions to open related mailings and received/opened/replied/clicked traces.

**mailing.trace**
- `model` and `res_id` are now indexed; `set_opened`, `set_clicked` and `set_replied` also update the related mailing contact dates.

## How your habits should change

- The favorite star is gone: use **Save as template** in the cog menu, then start new mailings from the **Templates** menu.
- "Favorite Filters" are now "Dynamic Lists", selected as tags rather than through a single dropdown.
- One mailing can combine several dynamic lists; recipients are the union of their rules.
- You can save a hand-crafted domain directly as a dynamic list from the domain editor (save the mailing first).
- Check the displayed recipient count before sending to validate your targeting.
- Contact records now show engagement ratios and last open/click/reply dates.

## What you gain by migrating

- Reusable, shareable recipient targeting, with no domain rebuilding for each campaign.
- A genuine template library replacing the hidden favorite mechanism, making design reuse and standardisation easier.
- Stronger campaign insight: per-contact ratios and last-interaction dates, plus multi-mailing statistics.
- A cleaner data model: the obsolete favorites/filter fields are removed, and Odoo provides upgrade scripts, so migrating in 20.0 avoids keeping 19.0 technical debt.
