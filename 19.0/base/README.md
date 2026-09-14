# base migration guide (18.0 -> 19.0)

## What's new for users

The 19.0 release notes mostly describe Enterprise apps and localizations. For the `base` addon (Community), the user-visible novelties are:

- **Readable dates everywhere**: dates are shown in an abbreviated form (e.g. "Aug 1, 2025" instead of 08/01/2025). Language date, time and thousand-separator formats are now picked from lists instead of typed as free text.
- **Faster navigation**: data and translations fetched while browsing are cached, so coming back to a view is faster, and the control panel (search, view switcher) is usable immediately while data loads.
- **Incremental mass edit**: in a list view, numeric fields now accept `+=`, `-=`, `*=` and `/=` (e.g. select all products and type `*=1.1`).
- **Properties traced in the chatter**: when a record's parent changes, its property values are logged in the chatter.
- **Cleaner search dropdowns** thanks to basic text formatting.

## Technical data model changes

**Renamed fields**
- `res.users.groups_id` → `group_ids`; new `all_group_ids` = the user's groups plus implied ones.
- `res.groups.users` → `user_ids`; new `all_user_ids`.
- `groups_id` → `group_ids` on window actions, server actions, reports, `ir.ui.menu` and `ir.ui.view`.
- `ir.filters.user_id` (many2one) → `user_ids` (many2many; empty means shared with everyone).

**Groups and implications**
- Odoo no longer writes users into implied groups; membership is not materialized anymore. `all_group_ids` / `all_user_ids` rely on the reflexive transitive closure (`implied_ids`, `all_implied_ids`, `implied_by_ids`, `all_implied_by_ids`, `disjoint_ids`).
- `_check_one_user_type` becomes `_check_disjoint_groups` (users) and `_check_user_disjoint_groups` (groups).
- `res.groups.category_id` is replaced by `privilege_id`, pointing to the new `res.groups.privilege` model (name, description, placeholder, sequence, category, groups). `ir.module.category.group_ids` becomes `privilege_ids`. `res.groups` gains `sequence`; the `color` / `visible` helpers are removed.
- The user form is now built by a JS widget (`view_group_hierarchy`): the generated `in_group_*` / `sel_groups_*` pseudo-fields are gone, and `res.users` gains a `role` field (Member / Administrator).

**Other models**
- New: `res.groups.privilege`, `properties.base.definition`, `properties.base.definition.mixin` (used by `res.partner`).
- Removed: `res.partner.title` and the `title` field on contacts.
- Server actions: `child_ids` is now a one2many (`parent_id`), the name is computed (`automated_name`), and "Execute Existing Actions" is renamed "Multi Actions".
- `res.lang`: `short_date_format` and `short_time_format` are removed; `date_format`, `time_format` and `grouping` become selections.
- `ir.filters`: `create_or_replace` is replaced by `create_filter`; the unique-name constraints give way to an index.

## How your habits should change

- Stop typing custom date/time formats: choose a format in the language settings.
- Use the new list-view operators for bulk price or quantity adjustments instead of editing line by line.
- Expect dates to read like "Aug 1, 2025" on screens, reports and exports.
- As an administrator, expect a redesigned Users form where groups are shown by privilege; the old checkbox/selection pseudo-fields are no longer available in exports or Studio automations.

## What you gain by migrating

- A simpler, more consistent date and number experience across all apps.
- Noticeably faster list and form navigation thanks to caching, with an immediately available control panel.
- A modern user and group administration screen, with clearer visibility of implied, disjoint and automatically granted groups.
- A cleaner access-rights model (privileges, non-materialized implied groups) that is easier to audit and to extend.
- An up-to-date Community code base, aligned with the rest of the 19.0 ecosystem (including chatter tracking of property changes).
