# project migration guide (17.0 -> 18.0)

## What's new for users

- **Analytic plans on projects** — the headline change. Allocate project costs and revenues to several analytic plans (one account per plan) instead of a single analytic account. Stock pickings, purchase orders and manufacturing orders can be linked to a project for cost tracking, and delivered products can be re-invoiced automatically.
- **Project sharing** — portal users can be granted edit access to *all* project tasks or only the ones they follow. External collaborators can follow/unfollow tasks, and can be @mentioned.
- **Top bar navigation** — reach related records (timesheets, invoices…) straight from the task view through embedded control-panel actions; add custom actions, private or shared.
- **Tracking revisions in tasks** — follow changes to a task description and revert to an earlier version.
- **Sub-tasks from Kanban** — create sub-tasks directly from task cards.
- **Project updates become a Dashboard**, with burn-up analysis.
- Allocated hours on tasks now follow the planned dates.

Note: the release notes also mention Gantt capabilities (workload progress bar, deadline display, batch scheduling). Those belong to the Enterprise edition and are not part of this Community addon.

## Technical data model changes

**project.project**
- `analytic_account_id` removed, replaced by `account_id` plus the analytic distribution of `analytic.plan.fields.mixin` (one account per plan). `analytic_account_balance` now relates to `account_id.balance`.
- Deprecated fields `doc_count` and `allow_rating` removed.
- New computes/actions: open and closed task counts, `task_completion_percentage`, `total_update_ids`, `_add_collaborators(partners, limited_access=False)`, `_add_followers`, `message_unsubscribe`, `action_open_share_project_wizard`, `copy_data` (replacing `copy`).

**project.task**
- `analytic_account_id` removed: tasks no longer carry an analytic account; timesheets take the accounts of their project, or of the sale order line distribution.
- New fields/computes: `is_closed`, `display_in_project`, `depend_on_count`, `link_preview_name`, subtask completion percentage, follow button, `project_sharing_toggle_is_follower`, `action_archive`.
- `_read_group_stage_ids` and the personal-stage grouping lose their `order` argument.

**project.task.type** — `description` removed; personal-stage handling refactored (`copy` → `copy_data`).
**project.tags** — `_name_search` replaced by `name_search`.
**res.config.settings** — the `analytic_plan_id` setting is removed.
**account.analytic.account** — `project_ids` now linked through `project.account_id`.
**res.partner** — new company-consistency helpers on projects/tasks.

## How your habits should change

- Set analytic accounting on the **project**, not on each task: use the Analytic notebook, one account per plan.
- Tasks have no analytic account of their own; timesheet costs follow the project, or the sale order line distribution when one is defined.
- "Project Updates" are now the **Dashboard**.
- Creating a project in the Kanban view no longer adds you to its favorites.
- When sharing a project, choose between full edit access and "tasks they follow"; unfollowing a project removes the user from its collaborators.
- Task stages no longer have a description field.

## What you gain by migrating

- **Multi-dimensional profitability**: costs and revenues are no longer stuck on one analytic account, so you can report per team, department or service as well as per project.
- **Stock, purchase and manufacturing flows feed project profitability** and can be re-invoiced to the customer, instead of being tracked by hand.
- **Cleaner collaboration**: fine-grained sharing rights, task following and revision tracking keep external contributors under control.
- **A consolidated project Dashboard** replacing scattered updates, and task planning aligned on real dates.
