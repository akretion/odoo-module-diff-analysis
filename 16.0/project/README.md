# project migration guide (15.0 -> 16.0)

## What's new for users

What is relevant to this Community addon in the 16.0 release notes:

- **Recurring tasks**: the planned date of a recurring task is now computed automatically, so you spend less time rescheduling.
- **SMS on stages**: a task stage can trigger an SMS template automatically when a task reaches it (requires the SMS app).
- **Cleaner tags**: the auto-complete on task tags only proposes tags already used in that project.
- **Open reporting**: any project user can open the Reporting menu and sees the data their access rights allow.

The notes also mention a Documents workspace, resource allocation in the Gantt view and smart batch scheduling. Those belong to Enterprise apps and are NOT part of this Community addon.

## Technical data model changes

Coming from the 15.0 -> 16.0 source diff (2 commits, no method signature change):

**Closing stages redefined**
- `project.task.type.is_closed` is removed.
- `project.task.is_closed` still exists but is now related to `stage_id.fold` ("Folded in Kanban stages are closing stages") instead of `stage_id.is_closed`.
- Default-stage search and `update_date_end()` now rely on `fold` only.

**Analytic tags removed ("Analytic Apocalypse")**
- `analytic_tag_ids` removed from `project.project` and `project.task`.
- The extension of `account.analytic.tag` adding `task_ids` / `project_ids` is deleted.
- Tags used to distribute costs are replaced by the analytic distribution widget, and tag groups by analytic plans.
- New `res.company.analytic_plan_id` ("Default Plan") with compute `_compute_analytic_plan_id`.
- New `res.config.settings.analytic_plan_id` (related to the company).
- Analytic accounts created for a project now receive the company's default plan.

**Other**
- Copying a task also copies its sub-tasks when `allow_subtasks` is set.
- Unarchiving a stage unarchives its tasks.
- People following a project are now also subscribed to its project updates.
- New model method `project.task.get_unusual_days(date_from, date_to=None)` (used by the task calendar view).
- No changed method signature was detected; the removed `action_view_kanban_project` was an empty placeholder.

## How your habits should change

- Closing a stage: forget the "Closing Stage" checkbox — fold the stage in Kanban instead; that is what makes it a closing stage.
- Analytic accounting: stop tagging projects/tasks to dispatch costs. Configure analytic plans and distributions, set a company Default Plan, and let project analytic accounts inherit it.
- Duplicating a task now also duplicates its sub-tasks; check the result before sharing.
- Unarchiving a stage brings all its archived tasks back.
- Following a project also subscribes you to its updates.
- In the task chatter you may receive a new "Task in Progress" notification, and "Stage Changed" appears above "Task Blocked".
- The kanban burger menu entry "Edit" is now "View".
- Deleting a task is no longer blocked by a sale order validation error.

## What you gain by migrating

- Clearer semantics: one single notion of closing stage (folded), consistent everywhere in kanban, defaults and dates.
- Less manual work: auto-planned recurring tasks, duplicated sub-tasks, project-scoped tag suggestions, SMS on stage change.
- Simpler, more powerful analytics: distribution widget plus plans replace confusing tags, with a sensible default plan per company.
- Reporting opened to all project users.
- A maintained branch: 15.0 no longer evolves, while 16.0 keeps getting fixes and prepares you for future upgrades.
