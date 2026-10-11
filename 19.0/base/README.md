# base migration guide (18.0 -> 19.0)

## What's new for users

- **Dates are readable.** Odoo 19 displays abbreviated dates (e.g. *Aug 1, 2025* instead of *08/01/2025*). Date widgets show a readable value and switch to an input on focus.
- **Language settings are simpler.** In *Settings > Translations > Languages*, Date Format, Time Format and Separator Format are now dropdown selections (with examples) instead of free-text format strings. The "Short Date Format" and "Short Time Format" fields are gone.
- **The Users form has been rebuilt.** Groups are no longer a grid of generated checkboxes: a dedicated widget shows the group hierarchy, highlights groups added automatically by implication, warns about exclusive (disjoint) groups, and can reveal technical groups with a *Show technical groups* toggle. A new **Role** field (Member / Administrator) lets you assign the standard user type in one click. The old "Access Rights Mismatch" warning text field is gone.
- **Saved filters (favorites).** The *Shared* checkbox is removed. You now select the users to share a favorite with; leaving it empty shares it with everyone, and a new favorite stays private to you by default. An *Edit* button next to *Save* opens the favorite's form directly.
- **Multi Actions server actions** now edit their child actions inline in a kanban-style list instead of a many-to-many selection. Action names are generated from the configuration (and stay editable).
- **Contacts:** the *Title* model is removed, so titles are no longer selectable on contacts. Address types are relabelled *Invoice*, *Delivery* and *Other*.
- **Import:** any file format is accepted for binary fields.

Note: the release-notes sections on Localizations, Industries, ESG, Payroll, etc. do not concern the `base` module.

## Technical data model changes

- `res.users`: `groups_id` → **`group_ids`** (explicitly assigned groups) and new computed/searchable **`all_group_ids`** (groups + implied groups). New `role` selection, `view_group_hierarchy`, `view_show_technical_groups`, `view_disjoint_group_ids`, `view_all_disjoint_group_ids`, `view_visible_implied_group_ids`.
- `res.groups`: `users` → **`user_ids`** + **`all_user_ids`**; `trans_implied_ids` → **`all_implied_ids`**; new `implied_by_ids`, `all_implied_by_ids`, `disjoint_ids`, `sequence`, `privilege_id`. `category_id` is removed; the uniqueness constraint becomes `UNIQUE (privilege_id, name)`.
- New model **`res.groups.privilege`** (name, description, placeholder, sequence, category_id, group_ids) grouping the groups displayed in the user form.
- `ir.module.category`: `group_ids` → **`privilege_ids`**; order becomes `sequence, name, id`.
- Field renamed `groups_id` → **`group_ids`** on `ir.actions.act_window`, `ir.actions.server`, `ir.actions.report`, `ir.ui.menu` and `ir.ui.view`.
- `ir.actions.server`: `child_ids` changes from Many2many to **One2many** with new `parent_id` (cascade); `name` becomes computed/stored/readonly=False with `automated_name`; type label "Execute Existing Actions" → "Multi Actions".
- `ir.filters`: `user_id` (many2one) → **`user_ids`** (many2many); unique-name constraint and global-default checks removed; `create_or_replace` → `create_filter`.
- `res.lang`: `short_date_format` and `short_time_format` removed; `date_format`, `time_format`, `grouping` become Selection fields.
- `res.partner`: model `res.partner.title` and its `title` field removed; inherits the new `properties.base.definition.mixin`.
- New models `properties.base.definition` and abstract `properties.base.definition.mixin` (properties with no parent record).
- Python-side renames: `_Unknown` → `Unknown` (`_name = '_unknown'`), `FormatVATLabelMixin` → `FormatVatLabelMixin`, type annotations `Partner` → `ResPartner`, etc. `res.users` PHP-style split classes (`UsersImplied`, `APIKeysUser`, `UsersView`) were merged; `_check_one_user_type` → `_check_disjoint_groups`; reified-group helpers (`in_group_X`, `sel_groups_X`, `_remove_reified_groups`, `get_groups_by_application`, `_update_user_groups_view`) were deleted.

## How your habits should change

- Administer groups through the new widget rather than checkbox grids; users are no longer physically added to every implied group.
- Update your XML/data and custom modules: `groups_id` → `group_ids`, `users` → `user_ids`, `trans_implied_ids` → `all_implied_ids`, `category_id` → `privilege_id`, `ir.filters.user_id` → `user_ids`, server-action `child_ids` written as `parent_id`.

## What you gain by migrating

- **Performance:** documented query reduction when changing a user's groups (26 → 17 queries in `base`, 40 → 29 across modules) and less cache invalidation.
