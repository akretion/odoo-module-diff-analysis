# project migration guide (18.0 -> 19.0)

## What's new for users

The 19.0 release notes are mostly about other apps (industry packages, localizations) and Enterprise-only features (auto-planning on flexible schedules, Gantt view on the portal, drag & drop in the calendar). For this Community addon, the relevant points are:

- **Assignees from email**: contacts placed in the "To" field of an email that creates a task are now added as assignees.
- **Reusable templates**: project and task templates support project roles — when you create a project from a template, you choose the employee for each role and they are added to the tasks.
- **Task priorities**: several priority levels (Low, Medium, High, Urgent) are available, and tags, assignees and priority can still be set straight in the task title with `#tag`, `@user`, `!`, `!!`, `!!!`.

Everything else you will notice is a cleanup of existing behaviour (see below).

## Technical data model changes

**Global settings are gone**
- `res.config.settings` no longer defines `group_project_rating`, `group_project_recurring_tasks`, `group_project_task_dependencies` and `group_project_milestone`.
- `project.project.allow_task_dependencies` and `allow_milestones` no longer default from the user's groups; a new `allow_recurring_tasks` field is added. The three options are now per project.
- The related user group is added/removed automatically depending on whether any project (archived included) uses the feature (`_check_project_group_with_field`, `_check_project_group_at_removal`, `check_features_enabled`).

**Customer ratings move from the project to the task stage**
- `project.project` loses `rating_active`, `rating_status`, `rating_status_period`, `rating_request_deadline` and the daily `_
