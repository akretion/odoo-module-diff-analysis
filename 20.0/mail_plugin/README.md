# mail_plugin migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes do **not** describe this add-on directly: `mail_plugin` is not covered by any functional section (Sales, eCommerce, Email Marketing, Localizations, Accounting, ...), which document other apps. The relevant change is stated in the add-on itself:

**The enrichment feature has been removed from the add-in.** Upstream considered enrichment slow and annoying in daily use, and 20.0 refocuses the plugin on the logging part only (logging emails and meetings into Odoo chatter), simplifying everything on the Odoo side.

Two general mail-framework items from the 20.0 release notes are worth knowing, because they surround the messages your plugin logs:

- **In-body tracking:** tracking values are no longer stored; the tracking message is generated on the fly. Features based on stored tracking values (burndown charts, stage duration) were reworked, and a dedicated module is needed if you still require stored values. This affects record chatter, not the add-in.
- **Source of postings:** mail records now indicate the source of their content, making it easier to audit what was logged through an add-in versus typed directly in Odoo.

Since enrichment is gone, the plugin no longer spends IAP credits, and no enrichment lookup is triggered from your mailbox.

## Technical data model changes

Only removals, no additions:

- Deleted files: `models/res_partner.py` and `models/res_partner_iap.py`; `models/__init__.py` no longer imports them.
- `res.partner` no longer inherits the enrichment fields. The removed fields were `iap_enrich_info` (Text) and `iap_search_domain` (Char), both non-stored computed fields reading from `res.partner.iap`.
- The `res.partner.iap` model (`Partner IAP`) is removed entirely. It held `partner_id` (Many2one to `res.partner`, cascade, required), `iap_search_domain` (Char), `iap_enrich_info` (Text, read-only) and the constraint `_unique_partner_id` (`UNIQUE(partner_id)`).
- The `res.partner` `create` and `write` overrides are removed. In 19.0 they mirrored enrichment values into `res.partner.iap` (as superuser), creating one row per partner and updating it on write. In 20.0 no such row is created or updated.
- No new model, field, or method signature was introduced for this add-on.

Consequence for your data: the stored IAP responses and search domains have no equivalent field in 20.0. If you need that history, export `res.partner.iap` before upgrading. Custom code, Studio automations, server actions, exports, filters and report templates that read `iap_enrich_info`, `iap_search_domain` or `res.partner.iap` will break and must be removed or adapted.

## How your habits should change

- Stop expecting a company lookup or enrichment panel on contacts. Only the logging workflow remains: file emails and meetings into Odoo from Outlook/Gmail.
- Do not rely on the contact form to display IAP enrichment results; that information is no longer available in Odoo.
- Re-check any saved filters, group-bys, list exports, pivot/graph views or automations that referenced the two removed fields or the `Partner IAP` model — they will raise errors after migration.
- Clean up customizations that wrote to those fields: they will now fail silently at best, or block the write at worst.
- If enrichment data matters to your business, plan an alternative (external data provider or manual research), because the feature is intentionally removed upstream and will not be restored by configuration.

## What you gain by migrating

- A faster, less intrusive add-in: no more slow enrichment round trips when using the plugin.
- A lighter `res.partner`: two computed fields and one technical model disappear from the model you use every day.
- No IAP credit consumption and no duplicated JSON payloads stored per contact.
- Less customization surface: fewer fields to maintain, test and document across upgrades.
- Staying aligned with upstream code, which keeps the module supported and future upgrades cheaper.
- A clear, single purpose for the plugin — email and meeting logging — which makes user training shorter and behaviour more predictable.
