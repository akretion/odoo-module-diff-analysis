# base migration guide (17.0 -> 18.0)

## What's new for users

The 18.0 release notes are mostly about Accounting, Payroll, Website, etc. Only a few items genuinely target `base`:

- **Device and session management** (base): each user can now see which devices/sessions are active on their account, with platform, browser, IP and last activity. A device can be revoked: the corresponding session is destroyed, so a suspicious device can be blocked immediately. Administrators can see all devices.
- **Embedded actions** (base + web): actions can now host "embedded actions", displayed in the control panel behind a sliders button, to toggle between related views without leaving the current record (for example a project's tasks, timesheets and invoices). Default embedded actions come from the app; users with the rights can save their own, share them or keep them private, and reorder their own list. Default embedded actions cannot be deleted, only hidden.
- **Country-aware company form**: the company form now uses the same address formatting and VAT labelling mechanism as contacts, so the VAT field takes the label of the active company's country.

The release-notes extract also covers Payroll, eCommerce, Website, Subscriptions, Spreadsheet, Field Service, Appointments and localizations: those belong to other apps (mostly Enterprise), not to base.

## Technical data model changes

- `ir.property` is removed. Company-dependent fields are no longer non-stored/computed values in a side table: they are real stored fields whose column is `jsonb` (`{"1": value}` per company).
- Fallbacks (defaults) of company-dependent fields now live in `ir.default.json_value` instead of `ir.property` rows with `res_id IS NULL`; `ir.default` now invalidates caches on create/write/unlink.
- New readonly field `ir.model.fields.company_dependent`.
- Company-dependent many2one fields now support `ondelete='restrict'` (previously only `set null`) and are indexed by default (`btree_not_null`). Side effect: a model referenced by such a field can no longer have any `ondelete='cascade'` field — developers must override `unlink`.
- `_field_to_sql` now works on company-dependent fields, so they can be used in SQL/report generation.
- New model `ir.embedded.actions` (name, sequence, parent_action_id, parent_res_model/parent_res_id, action_id or python_method, user_id, default_view_mode, filter_ids, domain, context, groups_ids, is_visible, is_deletable). `ir.actions.act_window` gains `embedded_action_ids`.
- `ir.filters` gains `embedded_action_id` and `embedded_parent_res_id`; `_get_action_domain` and `get_filters` signatures are extended and uniqueness constraints updated.
- New models `res.device` (SQL view) and `res.device.log`; `res.users.device_ids`.
- New mixin `format.vat.label.mixin`. `res.company` now inherits `format.address.mixin` + `format.vat.label.mixin`; `res.partner` also inherits it (its `_get_view` VAT logic moved there).
- `ir.actions.act_window` readable fields now include `embedded_action_ids` and `close_on_report_download`.
- Field type annotations added (`parent_id: Partner = fields.Many2one(...)`): typing only, no behaviour change. `res.partner.self` uses an explicit comodel name.

## How your habits should change

- Admin screens built around "properties" disappear: company-specific defaults are now ordinary default values. Reports, group-bys and pivots on company-dependent fields finally behave like on any other stored field.
- Existing property values are converted by the upgrade scripts; nothing to re-encode by hand.
- Filters saved inside an embedded action stay attached to that embedded action (and are deleted with it) rather than mixing with the action's regular list filters.
- Saved views/filters per embedded action are personal unless you share them.

## What you gain by migrating

- A simpler, faster model: one less technical table (`ir.property`), less cache invalidation, cleaner default handling.
- Real reporting on company-dependent fields (group by, pivot, SQL).
- Security: users and administrators can review and kill active devices/sessions.
- Richer navigation with embedded actions in the control panel, without leaving the record.
- Better multi-country UX: VAT labels and address formats follow the company's country.
