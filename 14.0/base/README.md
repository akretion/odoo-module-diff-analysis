# base migration guide (13.0 -> 14.0)

## What's new for users

The official 14.0 release notes cover functional apps (Sales, Accounting, Inventory, Employees, Website, Project, ...). **No highlighted item targets the `base` module**, which is Odoo's technical foundation (users, groups, views, translations, external identifiers, bank data). Several of the listed items (Studio approval rules, custom views, map view) are Odoo Enterprise features and are not part of this Community deployment — they must not be counted as part of this migration.

So 14.0 is not about new buttons in `base`: it is about a reworked view engine that makes your existing screens faster and safer. Details below.

## Technical data model changes

- **`ir.ui.view`**: the technical field `model_ids` (link to `ir.model.data`) was removed. No functional data is lost.
- **`ir.model.data`** (External Identifiers): `date_init` and `date_update` were removed; the update timestamp is now the standard `write_date`.
- **`res.partner.bank`**: `qr_code_valid` ("Has all required arguments") was removed, together with `build_qr_code_url()` and `_validate_qr_code_arguments()`.
- **`ir.actions.report`**: `barcode()` gained a `mask=None` parameter, plus a new hook `get_available_barcode_masks()`. SEPA / Swiss QR-code generation now lives in dedicated modules (new `base_qr_code_sepa`) instead of `base`.
- **`ir.property`**: access rights were removed; only system users can reach those records. Use the private helpers `_get()`, `_get_multi()`, `_set_default()`, `_set_multi()`.
- **View API (developers)**: `postprocess_and_fields(node, model=None, validate=False)` replaces `postprocess_and_fields(model, node, view_id)`; `raise_view_error(message, view_id)` becomes `handle_view_error(message, raise_exception=True)`; `get_inheriting_views_arch(model)` loses `view_id`; `apply_view_inheritance(source, model)`; `_name_search(name='')`. A new `NameManager` object centralizes field and action validation.

## How your habits should change

- **Custom views are now checked much more strictly.** A label without `for`, a button whose method does not exist (or is private, starting with `_`), a field used in a `domain`, `attrs`, `context` or `decoration-` but absent from the view, a page outside a notebook, a wrong accessibility role... all now raise a blocking error when the view is saved or a module is installed/updated. Plan a clean-up of custom views before upgrading.
- **External Identifiers**: the Init Date / Update Date columns disappear; use Last Modification.
- **Properties (`ir.property`)** are no longer visible to non-system users; they are managed from Settings and technical menus.
- **Bank accounts**: the "QR code ready" flag is gone; QR codes are produced by reports instead.
- **Developers**: adapt overrides of the methods listed above; validation logic now lives in `NameManager` and in per-tag `_validate_tag_*` / `_postprocess_tag_*` handlers.

## What you gain by migrating

- **Faster installs and upgrades**: validation was regrouped and the inheriting-view search uses one recursive query — average module installation is ~25% faster and view combination (`read_combined`) is nearly twice as fast. Large settings and asset views benefit most.
- **Errors caught at validation time, not in production**: broken buttons and unknown fields in domains and filters are reported when the view is saved, with clearer messages showing the view name, XML ID, model and parent view.
- **Better accessibility**: automatic checks on aria roles, `alt` attributes, icon-only buttons, tabs, modals and progress bars.
- **More flexible QR/barcode generation**: masks and dedicated modules make QR codes reusable in payments, invoices and e-commerce.
- **Tighter security** on property values, now reserved to system users.
