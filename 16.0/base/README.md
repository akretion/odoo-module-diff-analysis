# base migration guide (15.0 -> 16.0)

## What's new for users

The official 16.0 release notes contain **no entry specific to the `base` module**: they focus on business apps (Accounting, CRM, Manufacturing, Website, Payroll, Spreadsheet, Time Off...). Most of those items are app-level and several are Enterprise-only, so none is claimed here.

For functional users, the only 16.0 novelties that touch `base` come from the core translation rework summarised in the next two sections. In short: action names become translatable at framework level, filter names are no longer translated, and translations are edited on the record itself instead of in a separate "Translations" list.

## Technical data model changes

The `ir.translation` model is removed (14 fields deleted, e.g. `src`, `value`, `lang`, `res_id`, `type`, `module`, `state`, `comments`, `name`).

Translated fields no longer use a side table: their values are stored in a JSONB column on the model's own table, as `{lang_code: text}`, always containing `en_US`. Possible values: `NULL`, `{"en_US": "Foo"}`, `{"en_US": "Foo", "fr_FR": "Bar"}`.

Behaviour changes:
- writing `False` sets NULL, i.e. empty in all languages;
- writing `""` empties only the current language and keeps the others;
- computed **stored** translated fields are no longer supported;
- the missing-translations wizard is removed (it no longer makes sense).

Field-level changes:
- `ir.actions.actions.name` becomes translatable; the `name` redefinitions on each action sub-type are removed (same result for users, consistent column under PostgreSQL inheritance);
- `ir.filters.name` loses `translate=True`: a filter now has a single name;
- selection names (`ir.model.fields.selection.name`), constraint messages, module category names and access-right lookups are now stored/read as language-aware JSON.

Method and API moves: `get_field_string()` moved to `ir.model.fields` (with new `get_field_help()` and `get_field_selection()`); `_load_module_terms()` moved to `ir.module.module`; new `ir.http.get_translations_for_webclient()` and `get_web_translations_hash()`; code translations are no longer stored in the database (extracted from PO files and cached per worker).

## How your habits should change

- Stop using the Translations menu / `ir.translation` records to review or fix terms. Edit the translated value directly on the record, in the target language (translation mode). Code strings (Python/XML `_()`) now live only in the PO files.
- To change a code translation, update the PO file and reload the Odoo worker: it is no longer a database record you can edit live in the interface.
- Clearing a translation with an empty value no longer erases the other languages — review your data if you relied on that behaviour.
- Filter names are no longer translated: check saved filters whose names were translated before migrating.
- Action names are translated through the standard mechanism now: verify action labels after migration.
- Custom developments relying on `ir.translation`, on computed stored translated fields, on `get_field_string()` elsewhere or on `_load_module_terms()` must be adapted.
- Loading PO files is roughly twice as slow: plan extra time for the translation step of the migration.

## What you gain by migrating

- Reading translated fields is faster, and reading "terms"-style translations (views, HTML content) is much faster.
- `ilike` searches on translated fields are much faster and now work in **every installed language** when the field is trigram-indexed (previously only the English source could be indexed).
- Updates of translated fields trigger less ORM flushing, and translations now follow their record: deleting a record deletes its translations, no more orphan rows accumulating in `ir.translation`.
- The database gets lighter (one table less, no duplicated translation rows) and everyday multi-language work is more reliable.
- Code translations are held in memory (a few MB) and shared across registries, which makes translation handling simpler to operate and less database-dependent.
