# base migration guide (18.0 -> 19.0)

`base` is the core of Odoo: users, groups and access rights, partners, languages, actions. Odoo 19.0 reshapes how groups and rights are stored and displayed, and renames several widely used fields.

## What's new for users

- **Redesigned user form.** The generated checkbox/selection fields for groups are gone. Groups are now picked with a client-side widget grouped by Privilege/application; implied groups appear as "added automatically", and technical groups can be revealed on demand.
- **Configurable group display.** Administrators define Privileges to control how group sets are presented for each application.
- **Readable dates.** Formats such as "Aug 1, 2025" replace purely numeric ones. Date, time and number grouping formats are now chosen from predefined lists instead of raw format strings.
- **Contacts.** The contact "Title" model was removed; address types are simply labelled Invoice / Delivery / Other. Properties can be defined on contacts without a parent company, and property values are logged in the chatter when a parent record changes.
- **Partner autocomplete** now uses Dun & Bradstreet data (same feature, better quality).
- **Faster navigation:** browsing data and translations are cached, and the control panel/search appears before data loads.

## Technical data model changes

Groups and users
- `res.users.groups_id` → `group_ids` (explicitly assigned groups) plus new computed `all_group_ids` (groups and implied groups).
- `res.groups.users` → `user_ids` plus new computed `all_user_ids`.
- `res.groups.trans_implied_ids` replaced by `all_implied_ids`; new `all_implied_by_ids` and `disjoint_ids`. Implied relations now use the reflexive transitive closure.
- **Behaviour change:** users are no longer written into all their implied groups; effective rights are resolved dynamically.
- `_check_one_user_type` → `_check_disjoint_groups`; `_has_multiple_groups` removed.
- `groups_id` → `group_ids` also on `ir.actions.act_window`, `ir.actions.server`, `ir.actions.report`, `ir.ui.menu`, `ir.ui.view`.

New model `res.groups.privilege` (name, description, placeholder, sequence, category_id, group_ids). `res.groups.category_id` → `privilege_id`; `ir.module.category.group_ids` → `privilege_ids`; the unique constraint is now `(privilege_id, name)`.

`res.partner`: model `res.partner.title` and field `title` removed. New `properties.base.definition` model and `properties.base.definition.mixin`, inherited by `res.partner` (`properties`, `properties_base_definition_id`).

`ir.filters`: `user_id` (many2one) → `user_ids` (many2many); unique constraints replaced by an index; `create_or_replace()` → `create_filter()`.

`ir.actions.server`: `child_ids` from many2many to one2many with new `parent_id`; `name` becomes computed/stored (with `automated_name`); "Execute Existing Actions" renamed "Multi Actions"; children inherit model and groups.

`res.lang`: `short_date_format` and `short_time_format` removed; `date_format`, `time_format` and `grouping` become Selection fields.

Internal refactor: `UsersImplied` and `APIKeysUser` merged into `ResUsers` (fewer queries when writing groups), new `role` field, class renames.

## How your habits should change

- Use `group_ids` for what is explicitly assigned, `all_group_ids` for effective rights; implied groups are no longer stored as memberships.
- The user form no longer shows one checkbox per group: pick groups in the new selector and review the "added automatically" panel.
- A Member / Administrator role shortcut is available on the user form.
- Contacts: stop using titles; use name/function fields instead.
- Favourites/filters can be shared with selected users, or with everyone when left empty.
- Language formats are selected from the proposed lists.
- Any custom module, import or template using `groups_id`, `users` or a group's `category_id` must be adapted.

## What you gain by migrating

- A correct, simpler access-rights model: no duplicated implied-group rows, less database churn, faster user writes and fewer cache invalidations.
- A modern rights administration UI that functional admins can maintain without generated views.
- A cleaner core model: `res.partner.title` and reified group fields removed, `res.users` code consolidated.
- Declarative language formats, readable dates, finer filter sharing and contact properties.
- A supported, future-proof base API that new apps and OCA modules target in 19.0.
