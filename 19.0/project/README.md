# project migration guide (18.0 -> 19.0)

This guide summarises what changes in the **project** addon (Community edition) when moving from 18.0 to 19.0.

## What's new for users
- **Project templates**: create a project from a template with pre-filled details and roles defined on template tasks, choosing which user fills each role at creation. Task planning follows the template.
- **Task templates**: convert a task into a reusable template and create new tasks from it.
- **Assignees from email**: when a task is created from an incoming email, internal users found in the "To" field are set as assignees.
- **Tasks in the calendar**: tasks to plan appear in the calendar side panel and can be scheduled by drag & drop.
- **Customer ratings per stage**: rating requests are configured on the task stage instead of the project.
- **Project privacy**: an additional privacy level lets you share private projects with portal users.

## Technical data model changes

**project.project**
- Added: `allow_recurring_tasks`.
- Removed: `rating_active`, `rating_status`, `rating_status_period`, `rating_request_deadline` (moved to `project.task.type`).
- `allow_task_dependencies` / `allow_milestones` no longer get a default from user groups and gain inverse methods.
- New helpers: `_check_project_group_with_field`, `check_features_enabled`, `_get_project_features_mapping`, and template methods (`action_create_template_from_project`, `action_toggle_project_template_mode`, `action_create_from_template`, `action_undo_convert_to_template`, `get_template_tasks`).

**project.task.type**
- Added: `rating_active`, `rating_status`, `rating_status_period`, `rating_request_deadline`, `_send_rating_all`.
- Removed: `disabled_rating_warning`.

**project.task**
- `rating_active` is now related to `stage_id.rating_active`; `allow_recurring_tasks` is related to the project.
- `display_in_project` is computed only (depends on `project_id`, `parent_id`), no longer editable; `show_display_in_project` is removed.
- Subtasks are hidden by default: `_where_calc` drops the `display_in_project` leaf when the `show_subtasks` context key is set.
- Portal lists renamed `SELF_*_FIELDS` -> `TASK_PORTAL_READABLE_FIELDS` / `TASK_PORTAL_WRITABLE_FIELDS`; custom portal field-access checks removed.
- Template additions: `action_convert_to_template`, `_compute_has_template_ancestor`, `get_import_templates`, `plan_task_in_calendar`.
- `partner_phone` moved into this module.

**Others**
- `project.milestone`: `project_allow_milestones` added.
- `project.role`: colour default and `copy_data`.
- `res.config.settings`: `group_project_rating`, `group_project_recurring_tasks`, `group_project_task_dependencies`, `group_project_milestone` and `_get_basic_project_domain` removed.
- Core refactors: `read_group` -> `formatted_read_group` / `_read_group`, `name_search(args=)` -> `domain=`, `toggle_active` deprecated in favour of `action_unarchive`, mail overrides aligned (`msg_dict`, `msg_vals=False`, `_notify_get_reply_to(..., author_id)`).

## How your habits should change
- **Project options are per project.** Task Dependencies, Milestones and Recurring Tasks are no longer global settings; enable them in the project form. The related user group is granted automatically as soon as one project uses the option and revoked when no project does. New projects start with the three options disabled.
- **Ratings are per stage.** Configure "Send a customer rating request" on the task stage. Ratings are always visible in views and reporting, so there is no global switch anymore.
- **Subtasks.** The per-subtask eye icon is gone: subtasks are hidden by default, use the "Show Sub-tasks" filter (not available in My Tasks).
- **Consistency.** Disabling Recurring Tasks or Task Dependencies on a project resets the recurring flag / waiting state of its tasks.

## What you gain by migrating
- Templates and roles remove repetitive project setup and standardise delivery.
- Email-based task creation fills assignees automatically.
- Per-stage rating configuration is far more flexible than a project-wide setting.
- Fewer global settings: options live where they apply, with less hidden configuration.
- A cleaner data model (deprecated APIs replaced, simplified fields) makes future versions and custom developments easier.
