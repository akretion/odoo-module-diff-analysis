# mass_mailing migration guide (15.0 -> 16.0)

## What's new for users

The Odoo 16 release notes for the Email Marketing app mix Community and Enterprise features. Only the following concern the Community `mass_mailing` addon:

- **Save Filters** — the audience domain you build for a mailing can now be saved as a reusable filter (hollow star icon next to *Filter*) and reused on future mailings. Saved filters are managed from a new *Favorite Filters* menu under *Configuration*.
- **Contacts Mass Edit** — mailing contacts can be edited in bulk directly from the List view.
- **Mailing Contacts Import** — import contacts into a mailing list by pasting their email addresses, and download an import template showing the expected format.
- **List View** — sent and scheduled mailings appear in chronological order in the List view.
- **Mailing Reports** — the 24h stat mailing reports can be turned off in the settings.
- **Template Management** — mailings can be saved as templates so their design can be reused later.
- **Image Shapes** — shapes can be used to transform mailing images.

## Technical data model changes

**New model `mailing.filter`** ("Mailing Favorite Filters"): `name`, `mailing_domain`, `mailing_model_id` (ir.model, required, cascade), `mailing_model_name` (related) and a `create_uid` override. A constraint checks that the saved domain is valid for the recipient model.

**`mailing.mailing`**:

- Removed `source_id` (Many2one `utm.source`, required) and the `_inherits = {'utm.source': 'source_id'}` delegation. The model now inherits `utm.source.mixin`, so the UTM source is created automatically from the record's display name.
- Added `mailing_filter_id` (m2o `mailing.filter`) and `mailing_filter_domain` (related), with their computes and a model-consistency constraint. `_compute_mailing_domain` now depends on the saved filter; changing the recipient model resets it.
- `create()` no longer fills `name` with "subject + timestamp" and `copy()` no longer appends "(copy)": naming is handled by the mixin.
- A broad linting pass reorganized field declarations and signatures in `mailing.py`, `mailing_contact.py`, `mailing_list.py` and `res_config_settings.py` — no behaviour change expected.
- `MassMailingContact.default_get(self, fields)` now uses the `fields_list` parameter name (framework signature alignment).

## How your habits should change

- The *Source* field is gone from the mailing form. UTM names (campaign, medium, source) must now be unique, and the source is derived from the mailing itself. If you reused one source string across many mailings, check your UTM reporting after migration.
- Mailings are no longer auto-named "subject + timestamp": names come from the content and are uniquified automatically.
- To reuse an audience, build the domain, click the hollow star next to *Filter* and name it; reuse it through the *Favorite Filter* field or *Configuration > Favorite Filters*. A filter is tied to a recipient model and is cleared when you switch model.
- Onboard contacts faster: paste email addresses or use the import template, and mass-edit contacts from the List view.
- Save proven designs as templates, and switch the 24h stat reports on or off in Settings.

## What you gain by migrating

- Audience segmentation stops being a manual copy/paste job: favorite filters become reusable assets across mailings.
- Cleaner master data: no duplicate UTM sources, unique non-translatable UTM names.
- Faster contact onboarding: paste-import, import template, mass edit.
- Better readability (chronological list view) and easier reuse of validated designs.
- A supported version with the accumulated mailing and mail-thread bug fixes.
