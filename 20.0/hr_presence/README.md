# hr_presence migration guide (19.0 -> 20.0)

This addon powers the presence state (Present / Absent / Off-Hours) shown on employees and used by the HR presence kanban. In 20.0, one stored field is removed so that a single computed presence state can be filtered and grouped. All flows described below are **Community edition** features.

## What's new for users

The official 20.0 release notes contain **no dedicated section for `hr_presence`**, so everything below is derived from the code change (PR [odoo/odoo#235438](https://github.com/odoo/odoo/pull/235438), *allow filtering and grouping by presence state*).

Concretely, for a functional user:

- You can now **filter and group employees by presence state** (Present, Absent, Off-Hours) from the standard search bar and group-by menus, instead of relying on a duplicated stored value.
- The presence value you see is now the **single computed state**, so it can no longer drift out of sync with the real situation (the removed stored field sometimes held a wrong value).
- The **Set Present / Set Absent** actions on an employee are unchanged and still let you force a presence status manually.
- The presence kanban and HR presence control (email / IP) keep working as before.
- No new Enterprise-only setting is introduced by this change.

## Technical data model changes

- **Removed field**: `hr_presence_state_display` (stored `Selection`, default `out_of_working_hour`) on `hr.employee`. It existed only to allow group-by in the presence kanban.
- **Removed field**: the same `hr_presence_state_display` on `hr.employee.public`.
- `hr_presence_state` (computed, **non-stored**) becomes the only presence state. It now has a `search` implementation so it can be used in search view domains and group bys.
- `_compute_presence_state` no longer depends on `hr_presence_state_display` (dependency is now only `user_id.im_status`) and no longer copies the display value into `hr_presence_state` for manually flagged employees. The manual status is carried by `manually_set_present` / `manually_set_presence`.
- The `write()` override on `hr.employee` that set `manually_set_present = True` when `hr_presence_state_display` was written as `present` was removed.
- `_action_set_manual_presence()` no longer writes the display field.
- `_check_presence()` no longer synchronises each employee's display field.
- Fields kept: `ip_connected`, `manually_set_present`, `manually_set_presence`.

## How your habits should change

- In any **custom filter, group by, kanban view, saved filter, dashboard, spreadsheet or automation**, replace `hr_presence_state_display` with `hr_presence_state`. Anything still pointing to the removed field will fail after the upgrade.
- Do **not** write to a presence display field to mark someone present: use the Set Present / Set Absent buttons, or set `manually_set_present`.
- **Performance caution**: `hr_presence_state` is computed and not stored. Its search method is provided for search views and group bys; avoid using it in code loops, server actions, scheduled actions or very large domains.
- Values previously stored in the removed column are **not carried over**; reporting built on that historical column must be rebuilt on the computed state.
- If you use the standard screens without customisations, there is nothing to do.

## What you gain by migrating

- **One consistent presence state** across employees, kanban and reporting — no more stale or wrong stored values.
- **Native filtering and grouping** by presence state in search views, something 19.0 only approximated with a duplicate field.
- **Less duplicated data** in the database and one less field to keep in sync.
- A code base aligned with the current Odoo version: staying on 19.0 means no further corrections to this module, while 20.0 keeps you on the supported line with the simplified presence model.
