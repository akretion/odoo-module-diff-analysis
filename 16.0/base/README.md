# base migration guide (15.0 -> 16.0)

## What's new for users

The official 16.0 release notes selected for this review cover business apps (Accounting, CRM, Website, Manufacturing, Project, Payroll, Time Off, Events…). They contain **nothing specific to the `base` module**, which is Odoo's technical foundation — so nothing here should be read as a base feature. The real 16.0 news for base is invisible on screen but affects every multilingual user: **how translated data is stored**.

- Reading translated values is faster, so screens, lists and reports displayed in a non-English language are lighter.
- Searching translated text (a product name in French, a filter name in Dutch…) now works in every installed language, not only in English.
- Clearing a field in one language no longer destroys the translations already entered in the other languages.
- The old "insert missing translations" wizard has been removed; it no longer made sense.

## Technical data model changes

- **Model `ir.translation` is gone** (14 fields removed: `name`, `res_id`, `lang`, `type`, `src`, `value`, `module`, `state`, `comments`, …). Translated fields are now stored **in the model's own table, in a JSONB column**: a JSON dict mapping language codes to text, always containing `en_US` as fallback.
- **`name` is now translated once, at parent level**: `ir.actions.actions.name` becomes `translate=True`, and the duplicate translated `name` declarations on `ir.actions.act_window`, `act_url`, `server`, `client` and `ir.actions.report` are removed.
- **`ir.filters.name` is no longer translated** (was `translate=True`, now only `required`).
- **Code translations are no longer stored in the database**: they are extracted from PO files and cached per worker.
- **Stored computed translated fields are no longer supported** by the framework.
- Methods moved: `get_field_string`, `get_field_help`, `get_field_selection` → `ir.model.fields`; `_load_module_terms` → `ir.module.module`; `get_translations_for_webclient`, `get_web_translations_hash` → `ir.http`. In the import converter, `_get_translations` is replaced by `_get_boolean_translations` and `_get_selection_translations`.
- Performance: reading translated values is faster (structured / "terms" values much faster), `ilike` search on trigram-indexed translated fields is much faster, but **importing PO files is about 2× slower**.

## How your habits should change

- Writing **False** clears the value **in all languages**; writing an **empty string ""** empties it **only in the current language** and keeps the others. Choose deliberately.
- Stop using the **Translations** menu / `ir.translation` to manage record translations: translations are edited directly on the record (translation icon) and read from the record's own translated column.
- Any manual SQL, script or import that wrote into `ir.translation` must be reworked; use standard imports on the translated field instead.
- **Code translations cannot be changed in the UI**: update the module's PO file and reload the workers (restart the server).
- For callable translations (HTML/XML content such as views and report bodies), keep the same structure in every language.

## What you gain by migrating

- A faster, more consistent multilingual experience for every user.
- Searches that find records by their translated names, in any language.
- A simpler database: no huge translation table to join, clean and purge, and no orphan "missing translation" rows.
- A clean technical foundation: 16.0 apps and future upgrades rely on this new translation model, so migrating now avoids a bigger jump later.
