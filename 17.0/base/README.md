# base migration guide (16.0 -> 17.0)

Odoo 17.0 reworks the base addon's record-naming API, moves company onboarding out of `res.company`, and simplifies Server Actions / Automated Actions.

## What's new for users

The official 17.0 release notes extract selected for this guide says nothing specific about the `base` addon: it covers business apps (Sales, Accounting, Inventory, POS, Payroll, Spreadsheet, ...) and localizations, including Enterprise-only features that are out of scope for a Community migration. The base addon's own changes are described here.

User-visible consequences of the base changes:

- Record names shown in lists, menus and dropdowns (contacts, banks, currencies, states, tags, model fields, technical data) look the same, but are now produced by one mechanism, so they are consistent between the web client, imports and reports.
- Automated Actions and Server Actions have a clearer editing screen: an automated action can hold several actions directly, and new ready-to-use triggers are proposed depending on the model (stage, state, priority, user, tag, archive, creation/update timing).
- Company onboarding progress is no longer stored on the company: it now lives in the dedicated `onboarding` module, so onboarding panels can be shared and reused.

## Technical data model changes

`res.partner`
- Field `display_name` (stored, computed, indexed) replaced by `complete_name` (stored, computed, indexed).
- `display_name` becomes a non-stored computed field, context-dependent on `show_address`, `partner_show_db_id`, `address_inline`, `show_email`, `show_vat`.
- `_order` and `_rec_names_search` now use `complete_name`; new class constant `_complete_name_displayed_types`.
- `name_get`, `_get_name`, `_get_contact_name` removed, replaced by `_compute_display_name` / `_compute_complete_name`.

`res.company`
- Removed field `base_onboarding_company_state`.
- Removed methods `action_open_base_onboarding_company`, `set_onboarding_step_done`, `_get_and_update_onboarding_state`, `action_save_onboarding_company_step`.

`ir.actions.server`
- Removed model `ir.server.object.lines` and its `fields_lines` One2many.
- Added fields: `name` (stored computed, editable), `update_field_id`, `update_related_model_id`, `value`, `evaluation_type`, `resource_ref`, `selection_value`, `value_field_to_show`.
- `type` default changed from `object_write` to `code`; `usage` label is now "Type"; `crud_model_id` relabeled "Record to Create"; `groups_id` relabeled "Allowed Groups".

`name_get` removal across base
- Overrides replaced by `_compute_display_name` in `ir.model.fields`, `ir.model.data`, `ir.ui.menu`, `res.bank`, `res.partner.bank`, `res.config.settings`, `res.country.state`, `res.currency`, `res.partner.category`, `res.partner`.
- `name_create` on `ir.model` now returns `(id, display_name)`.
- Import matching (`ir.fields.converter`) resolves records by `display_name` instead of `name_get`.

## How your habits should change

- Wherever you read `record.name_get()[0][1]`, use `record.display_name` (or `read(['display_name'])`).
- Custom modules overriding `name_get` must override `_compute_display_name` instead, keeping `display_name` dependencies correct.
- On contacts, use `complete_name` when you need a stable, stored, searchable partner name; `display_name` now varies with the context.
- In automation, an "Execute several actions" action is no longer needed: add the actions directly on the automated action. Triggers "On Creation" and "On Update" are replaced by "On save" plus the new timing conditions.
- An "Update the record" server action writes a single field; record creation now uses `name_create`.
- Do not read or write onboarding state on `res.company`; use the `onboarding` module.

## What you gain by migrating

- One naming mechanism for all records: fewer inconsistencies between forms, lists, imports and reports.
- `display_name` is cached during a transaction and can depend on context, so it stays fast and correct.
- Simpler, less error-prone Automated and Server Actions configuration.
- A lighter `res.company` model, easier to maintain and upgrade.
- You stay on a supported version, with the 17.0 core refactors already applied.
