# project migration guide (19.0 -> 20.0)

## What's new for users

- **Collaborators are separated from followers.** You can now give a customer access to a project in their portal without adding them as a follower. Access is managed in the Project Sharing collaborators list, not in the chatter.
- **Read-only portal access now uses the full Project Sharing interface** instead of the basic portal page, and it is no longer lost when a portal user unfollows a task.
- **Assign tasks to portal users** who are followers of a project.
- **Task tracking in the user portal**: changes recorded in a task's chatter are shown in the portal.
- **Mobile: improved task header design.**

Note: most 20.0 release notes touching this area describe Enterprise-only or other-industry features (project profitability reports, printable Gantt task schedules, industry packages). Those are not part of this Community addon and are not covered here.

## Technical data model changes

**project.collaborator**
- Removed: `limited_access` (Boolean)
- Added: `access_mode` Selection `view` / `edit` / `advanced_edit`, required, default `view`

**project.project**
- Removed: `stage_id_color` (related to `stage_id.color`)
- Create/write now automatically creates a `project.collaborator` in `view` mode for the project's partner when visibility is *Portal* or *Invited users*; switching back to an internal visibility removes the collaborators.

**project.project.stage** and **project.task.type**
- Removed: `color` (stage colour is no longer stored or configurable)

**project.task**
- Removed: `email_cc` and the `mail.thread.cc` inheritance; `stage_id_color`; `current_user_same_company_partner`
- Field access lists changed: `partner_id`, `date_deadline` and `parent_id` moved from *writable* to *readable only* for portal/sharing users; `portal_can_edit` / `portal_can_advanced_edit` are now readable.

**project.update**
- Inherits `mail.thread` instead of `mail.thread.cc`

**Method changes**
- `_add_collaborators(partners, access_mode='advanced_edit')` replaces `_add_collaborators(partners, limited_access=False)`
- `_find_internal_users_from_address_mail(...)` now returns only internal user ids (previously a 3-tuple)
- `project_sharing_toggle_is_follower()` no longer requires write access

## How your habits should change

- Stop using follow/unfollow to grant or remove portal visibility. Add the customer as a Project Sharing collaborator and pick a mode: **View**, **Edit** or **Advanced Edit**.
- Only **Advanced Edit** can change a task's stage or priority. **Edit** covers name, description, tags, state and colour, but no longer the planned date.
- The planned date (Deadline) is no longer editable by portal/external users in any mode.
- Old "limited access" settings are converted by the upgrade script: check each collaborator's mode after migration, since `view` is the new default.
- Task CC email addresses are gone. Incoming email CC addresses now only serve to suggest recipients in the chatter.

## What you gain by migrating

- Predictable, explicit portal permissions instead of fragile follower-based access.
- A real Project Sharing board for read-only external users, so customers can see the whole project and collaborate properly.
- Less manual administration: collaborators are created automatically and permissions are revoked consistently when a project becomes internal.
- Cleaner email handling on tasks, with no more stale CC lists.
