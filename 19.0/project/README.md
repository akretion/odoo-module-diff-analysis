# project migration guide (18.0 -> 19.0)

## What's new for users

From the official 19.0 release notes, the points that concern the Community `project` app:

- **Task templates** – reusable task templates that standardise recurring setups.
- **Project templates** – create a project from a template with pre-filled details, assign roles to the template tasks, then pick at creation time which employee fills each role.
- **Drag & drop in the calendar view** – reschedule existing tasks by moving them in the calendar.

Not included in Community (Enterprise only): Gantt view for tasks on the portal, auto-planning on flexible schedules. Task templates for service products belong to `sale_project`.

Note: the refactoring that moved task templates into a dedicated model was reverted before the release. A task template is still a task flagged as a template, so nothing changes for you there.

## Technical data model changes

- **Task templates**: the new `project.task.template` model and `project.task.task_template_id` were dropped again by the revert. Templates remain in `project.task` (`is_template`, `has_template_ancestor`); project counters and views keep filtering on those flags.
- **Customer ratings moved to stages**: `project.task.type` now owns `rating_active`, `rating_status`, `rating_status_period` and `rating_request_deadline`, and the daily scheduler runs per stage. The same fields were removed from `project.project`, and `disabled_rating_warning` was removed from the stage.
- `project.task.rating_active` is now related to `stage_id.rating_active` (previously `project_id.rating_active`).
- **New project toggle** `project.project.allow_recurring_tasks`, alongside `allow_task_dependencies` and `allow_milestones`; all three now default to off and drive group membership (new helpers `_check_project_group_with_field`, `check_features_enabled`).
- **Global settings removed**: `group_project_rating`, `group_project_recurring_tasks`, `group_project_task_dependencies` and `group_project_milestone` are gone from `res.config.settings`.
- **Subtasks**: `project.task.show_display_in_project` removed; `display_in_project` is now computed only, with no manual override.
- `project.milestone.project_allow_milestones` added (computed, searchable).

## How your habits should change

- Enable Task Dependencies, Recurring Tasks and Milestones **on each project** (project form), not in Settings. A brand new project starts with all of them off.
- Configure customer ratings **on task stages**, not on the project: tick "Send a customer rating request", choose when it is sent (when reaching this stage, or periodically) and the email template.
- Feature groups are now maintained automatically: enabling a feature in a project gives access to it; disabling it in the last project using it removes it again. Turning off dependencies resets waiting tasks, turning off recurrence clears the recurrent flag.
- Sub-tasks no longer show up by default in task lists: use the new **"Show Sub-tasks" filter** instead of the removed per-task eye icon.

## What you gain by migrating

- Feature-by-feature, project-by-project configuration: no global switch imposing task dependencies, recurrence or milestones on every project.
- Ratings that fit your process: feedback is requested by stage, so different stages and projects can behave differently.
- Calmer task views: sub-tasks stay out of the way until you ask for them.
- You move onto the current series, where fixes and new features land.
