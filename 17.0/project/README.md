# project migration guide (16.0 -> 17.0)

## What's new for users

**Explicit task statuses.** The old kanban state (In Progress / Ready / Blocked) becomes a real status: In Progress, Changes Requested, Approved, Done, Canceled, and Waiting (set automatically for blocked tasks, not user-selectable). Tasks are marked done or canceled with one click, without moving them to a "closing" stage.

**Recurring tasks generated on completion.** A new occurrence is created as soon as the previous one is marked as done, so you can plan ahead instead of waiting for the due date. Deleting the last task stops the recurrence.

**Sub-tasks and blocking information on kanban cards.** A card shows its sub-task counter and whether the task is blocked by another one.

Note: the official 17.0 notes also list project-flavoured items that do **not** concern the Community `project` app (projects from sales orders, Gantt batch scheduling, down payments/profitability, Timesheets, Knowledge, To-do). They belong to other apps or to Odoo Enterprise and are not delivered by this upgrade.

## Technical data model changes

The addon is split into one file per model (`project_project.py`, `project_task.py`, `project_task_type.py`, `project_milestone.py`); no functional impact.

**project.task – added:** `state` (Selection, stored, tracked), `display_in_project` (a sub-task can keep its project while being hidden from the project's task list), `closed_subtask_count`.

**project.task – removed:** `kanban_state`, `kanban_state_label`, `is_blocked`, `is_closed`, `display_project_id`, `is_private`, `project_root_id`, `ancestor_id`, `email_from`, `partner_email`, `partner_is_company`, `manager_id`, `child_text`, `allow_subtasks`, `stage_display`, `project_analytic_account_id`, `is_analytic_account_id_changed`, plus the detailed recurrence fields (`repeat_number`, `repeat_on_month/year`, weekday booleans, `repeat_day/week/weekday/month`, `repeat_show_*`, `recurrence_update`, `recurrence_message`, `recurrence_template_id`).

**project.project – removed:** `partner_email`, `partner_phone`, `allow_subtasks`, `task_count_with_subtasks`; `task_count` now includes sub-tasks.

**project.task.type – removed:** `legend_blocked/legend_done/legend_normal`; `auto_validation_kanban_state` renamed `auto_validation_state`.

**project.task.recurrence – removed:** `task_template_id`, `next_recurrence_date`, `recurrence_left` and all detailed repetition settings. Only `repeat_interval`, `repeat_unit`, `repeat_type` (Forever/Until) and `repeat_until` remain. New helpers `_get_recurring_fields_to_copy`, `_get_recurring_fields_to_postpone`, `_get_last_task_id_per_recurrence_id`, `_create_next_occurrence`; `_get_next_recurring_dates` and `_new_task_values` are gone, and the recurrence cron is deleted.

**Other model changes:** `res.company.analytic_plan_id` and the `res_company.py` extension are removed – projects create their analytic account on the shared "Project" plan, configured via a setting.

**Behaviour changes:** `is_closed` was first derived from the state (Done/Canceled), then removed altogether: filters, counts and domains now use `state`. Customer ratings on a stage write `state` (Approved / Changes Requested) instead of kanban bullets. A task whose blocking task is still open moves to Waiting automatically and returns to In Progress when dependencies close; `is_blocked` is replaced by `is_blocked_by_dependences()`. A task is private only when it has neither project nor parent. The task analytic account always follows the project (no manual-override tracking), and tasks created by email now create the contact from the sender address. Duplicating a task also copies its stage.

## How your habits should change

- Close work with the Done / Canceled status, not by folding a stage: the stage is now only a workflow bucket.
- Recurrences are described as "every n days/weeks/months/years, forever or until a date". Repetition counts and weekday picking are gone.
- The next recurring task appears when you finish the current one – no need to wait for the deadline.
- Never delete the last task of a recurrence unless you mean to stop it: it is removed with the task.
- Blocked tasks switch to Waiting on their own; spot them on the kanban cards.
- Sub-tasks are always available; the per-project "Sub-tasks" option no longer exists.
- Customer email/phone are edited on the contact, not through shortcut fields on the project or task.

## What you gain by migrating

- One-click closing and readable statuses, including Approved / Changes Requested fed by customer ratings.
- Automatic Waiting status for blocked tasks: fewer forgotten dependencies, cleaner kanban boards.
