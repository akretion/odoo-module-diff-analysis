# utm migration guide (19.0 -> 20.0)

Scope: the `utm` addon (Community edition). Between 19.0 and 20.0 the UTM tracker model was cleaned up so that UTM data follows the way UTM tags are used everywhere else in the industry.

## What's new for users

- **UTM handling now aligns with industry standards.** In 19.0 and before, Odoo created UTM sources *on the fly*: every mailing, social post or similar record silently generated its own `utm.source`, named after that record. That is not how UTM tagging is normally used, and it produced confusing, barely usable UTM reporting.
- **Instead of auto-created sources, UTM details are mapped through a new "Reference" field.** You now reference existing, deliberate UTM data rather than letting each document spawn a new source.
- **A consequence you will notice immediately:** the UTM source lists stay short and meaningful, and source/medium/campaign reporting becomes comparable with external analytics tools.

Other items in the Odoo 20.0 release notes (click tracking, conditional content, dynamic mailing lists, favourite blocks, mailing template library, new fonts/templates, product and event snippets, social media accounts, full-screen editing, contact/subscription management) belong to the Email Marketing application, not to the `utm` addon, and are **not** part of the Community `utm` module. The only release-notes item relevant here is the UTM reference change.

## Technical data model changes

Changes come from the module diff, not from the release notes.

- **Removed abstract model `utm.source.mixin`** (`_description = 'UTM Source Mixin'`). It was the shared behaviour that let a record (mailing, social post, ...) own a UTM source and have its source automatically named from the record's content.
- **Removed fields carried by that mixin** — they disappear from every model that inherited it:
  - `name`: Char, related to `source_id.name`, `readonly=False`
  - `source_id`: Many2one to `utm.source`, `required=True`, `ondelete='restrict'`, `copy=False`
- **Removed methods / signatures on the mixin**: `default_get` (which filtered `name` out of the context values), `create` (`@api.model_create_multi`, batch creation of the missing `utm.source` records and injection of their ids), `write`, and `copy_data`.
- **Removed logic**: automatic source creation from `name`/`default_name`/`_generate_name`, unique-name generation via `_get_unique_names("utm.source", ...)`, the `ValueError` *"You cannot update multiple records with the same name. The name should be unique!"*, and the counter increment when duplicating a source.
- **The `utm.source` model itself is not modified by this change** (no field added or removed). Its existing protection against deleting a source that is still in use is kept.

## How your habits should change

- Stop expecting a source to appear by itself. Creating a mailing or a social post no longer creates or links a UTM source; you choose or create the source data yourself (via the reference mapping).
- Source names now come only from what you actually type or import — no more one source per document, named after the document.
- You can no longer rename a UTM source indirectly by renaming the related record, since the related `name` field is gone.
- The multi-record rename error ("You cannot update multiple records with the same name") no longer occurs: the field it protected no longer exists.
- Duplicating a record no longer duplicates and renames its source.
- Review anything that reads or writes `source_id` on those records: saved filters, exports, automated actions, and custom or third-party modules that inherited `utm.source.mixin`. Those custom modules must be adapted, as the mixin is gone.

## What you gain by migrating

- **Reporting you can trust**: source, medium and campaign now follow industry conventions, so UTM analysis is clear and comparable with external tools.
- **A clean source list**: no more hundreds of machine-generated sources cluttering your UTM data.
- **A simpler, more stable model**: one less mixin, fewer duplicate-name errors and fewer duplication surprises.
- **Alignment with Odoo 20.0 direction**: all UTM features and reports built on this cleaned-up data will behave consistently in the future.
- **A known, bounded migration**: the break is well identified (one abstract model and its two fields), so the audit of your custom modules is short and predictable.
