# project migration guide (13.0 -> 14.0)

Scope: the Community `project` addon, based on the code changes shipped between 13.0 and 14.0 and on the official release notes. Release-note items belonging to Enterprise apps (Planning, Field Service, analytic accounting, Sales/Timesheet billing) are not part of this Community addon and are out of scope here.

## What's new for users

**Private projects no longer require followers.** In 13.0 the only way to let somebody see a private project was to make that person a follower — which also subscribed them to every new task and generated a flood of notifications. From 14.0 you explicitly list the people who may see the project, whether they follow it or not.

**A new "Visible to" selector.** Depending on the project Visibility setting, you now see:
- *Invited internal users*: the employees allowed to see the project.
- *Invited portal users and all internal users*: the portal contacts allowed to see the project.

Internal and portal users are kept in two separate lists, so you cannot mix them by mistake.

**Clearer visibility labels.** "Invited employees" becomes "Invited internal users", "All employees" becomes "All internal users", and "Portal users and all employees" becomes "Invited portal users and all internal users". The underlying options are unchanged.

**Other Project improvements in 14.0** (release notes): sub-tasks can be enabled project by project, and data entered on a sub-task is no longer overwritten when it is linked to a parent task; recurring tasks can be generated and managed for routine activities; extra project templates/kanban examples and an improved onboarding tour help new users get started.

## Technical data model changes

Project (`project.project`):
- `privacy_visibility` labels rewritten (keys `followers`, `employees`, `portal` unchanged; default `portal`).
- New `allowed_internal_user_ids` (many2many `res.users`, internal users only, defaults to the current user) and new `allowed_portal_user_ids` (many2many `res.users`, portal users only), each with its own relation table.
- `allowed_user_ids` ("Visible to") is computed and inversed as the union of those two lists: writing to it dispatches each user to the right list.
- `create()` / `write()` no longer subscribe the customer contact; they add the contact's users to the allowed lists when the visibility is "portal".
- When users are removed from a project's allowed lists, they are also removed from the "Visible to" list of all its tasks.

Task (`project.task`):
- New stored, editable `allowed_user_ids` ("Visible to"), restricted to Project Managers, and a related `project_privacy_visibility`.
- It is computed from the project: internal users are added when the project is restricted to invited internal users, portal users when the visibility is portal; users of the other kind are removed.
- New constraint: portal users cannot be added to a task of a project that is not set to the portal visibility (validation error).
- `manager_id` now reads the project manager with elevated (sudo) rights.

No model was added or removed, and no method signature change was flagged for this addon: the whole 13.0 → 14.0 delta found by the analysis tool is the visibility rework above.

## How your habits should change

- Stop using "Follow" as a sharing mechanism for private projects. Set the Visibility, then fill in the allowed users lists.
- Portal users are no longer added automatically (except through the customer-contact rule); select them explicitly on each project.
- Removing someone from a project now removes them from its tasks too: review task-level "Visible to" exceptions before tightening a project.
- On private projects, the "View Task" link sent in emails is only displayed to internal users.
- Because task visibility is derived from the project, always set visibility at project level first.

## What you gain by migrating

- Fine-grained sharing: give access to exactly the right colleagues or customers without subscribing them to thousands of task notifications.
- A clean separation between internal and portal users, with dedicated fields and validation rules.
- Consistent, automatic propagation of access rights from projects to tasks.
- The 14.0 project usability improvements: per-project sub-tasks, recurring tasks, refreshed templates and onboarding.
