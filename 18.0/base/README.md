# base migration guide (17.0 -> 18.0)

## What's new for users

The official 18.0 release notes cover business apps (Accounting, Website, Payroll, localizations) and barely mention `base`. What does concern the core: the improved "Configure layout" wizard shown when printing documents, and clearer error messages when merging PDFs. Other user-visible changes come from the module itself:

- Devices and sessions: Odoo tracks the devices/sessions used to log in (`res.device`, `res.device.log`) with platform, browser, IP, country and last activity. Each user can view their own devices and revoke one; revoking deletes the session file server-side, logging that device out immediately. Administrators see all devices.
- API keys can now have an expiration date and are cleaned up automatically.
- Company-dependent fields (values stored per company) are now stored, searchable fields: they can be filtered, grouped and used in reports/pivots — impossible before.
- A field made required by a Python expression in a view is now added to the view automatically, and view inconsistencies are reported as warnings instead of blocking the view.
- The country-specific VAT label now also applies on companies, not only on contacts (new shared mixin).

## Technical data model changes

New models: `res.device.log`, `res.device` (SQL view over the logs), `ir.embedded.actions`, and the abstract mixin `format.vat.label.mixin`.

Removed models: `ir.property` (the old company-property store, replaced by jsonb columns on the target model, with fallbacks in `ir.default`) and the deprecated `res.config.installer`.

New/changed fields: `res.users.device_ids`; `ir.model.fields.company_dependent`; `close_on_report_download` added to the readable fields of `ir.actions.act_window`. `res.company` now inherits `format.address.mixin` and `format.vat.label.mixin`.

Behaviour changes: company-dependent fields become stored jsonb columns, support `ondelete='restrict'` for many2one, and are indexed (`btree_not_null`); their fallback values are stored in `ir.default.json_value`. Record duplication (copy) is now batch-capable on many models.

Signature changes (impacting custom modules): `res.users.authenticate/_login/_check_credentials` take a `credential` instead of a `password` (passkey support); `has_groups(group_spec)` replaces the old group checks; `_name_search` is replaced by `_search_display_name`; `res.lang.format()` no longer takes `monetary`; `_get_default_tree_view` becomes `_get_default_list_view`; `ir.cron` was reworked (job progress, failure count, rescheduling, admin notification); `ir.actions.report` gains `_get_layout`, `_get_report_url`, `get_paperformat_by_xmlid` and report domains; asset bundle APIs changed (`css()`/`js()` lose `is_minified`, bundle URLs no longer contain the attachment id).

## How your habits should change

- Views: write `<list>` instead of `<tree>`; the related validation/post-processing methods were renamed.
- After the upgrade, regenerate assets: the bundle URL format changed, so previously cached bundles are not reused.
- Review your API keys and set expiration dates; check your device list and revoke anything unexpected.
- Company-dependent defaults are now record defaults (`ir.default`): re-check your per-company default values and any custom code touching `ir.property`.
- Replaced deprecated helpers in custom modules: `read_combined`, `main_partner`, `open_parent`, `check_super`, `_email_send`, `open_company_edit_report`, `res.config.installer`.
- Developers: fields are now type-annotated and several internal methods take extra arguments (`node_info`, `credential`).

## What you gain by migrating

- Security: visible, revocable sessions; passkey-ready authentication; API keys with expiry.
- Performance: company-dependent fields are stored and indexed, so they work in searches, groupings and reports; assets are generated faster and XML templates load quicker.
- Cleaner core: deprecated models/APIs removed, cron jobs with progress and failure handling, clearer PDF/report error messages.
- A smoother view engine: required fields handled automatically, warnings instead of blocking errors, list views and embedded actions aligned with 18.0 conventions.
