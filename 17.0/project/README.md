# project migration guide (16.0 -> 17.0)

## What's new for users

- **Task statuses replace the old kanban bullets.** Every task now has a State: In Progress, Changes Requested, Approved, Done, Canceled and Waiting. Done and Canceled mean the task is closed.
- **Waiting is automatic.** If a task is blocked by a task that is not finished, it moves to Waiting, and returns to In Progress once the blocker is done.
- **Recurring tasks are generated on completion.** The next occurrence is created as soon as the previous one is marked done, instead of by a nightly scheduler. This makes planning ahead much easier.
- **Simpler recurrence rules.** Only "repeat every N days/weeks/months/years", forever or until a date; the schedule preview and the "this / this and future tasks" choice are gone.
- **Richer kanban cards.** Sub-task progress (closed/total), the parent task name and blocked/dependency information are visible directly on the card.

## Technical data model changes

**project.task** — added: `state` (Selection: 01_in_progress, 02_changes_requested, 03_approved, 1_done, 1_canceled, 04_waiting_normal), replacing `kanban_state` and `kanban_state_label`, and `closed_subtask_count`. Removed: `kanban_state`, `kanban_state_label`, `is_closed`, `is_blocked`, `display_project_id`, `partner_is_company`, `partner_email`, `manager_id`, `ancestor_id`, `email_from`, `is_analytic_account_id_changed`, `project_analytic_account_id`, `child_text`, `stage_display`, `allow_subtasks`, `task_count_with_subtasks`, plus the recurrence fields (`recurrence_update`, `recurrence_message`, `recurrence_template_id`, `repeat_number`, `repeat_on_month`, `repeat_on_year`, `mon`..`sun`, `repeat_day`, `repeat_week`, `repeat_weekday`, `repeat_month`, `repeat_show_*`). Behaviour: changing the stage or the project resets the state to In Progress; changing the analytic account on a project now applies to all its tasks (manually-changed tasks are no longer preserved); tasks created from email create a partner when the sender is unknown.

**project.project** — removed: `partner_email`, `partner_phone`, `task_count_with_subtasks`, `allow_subtasks` (sub-tasks are always available).

**project.task.type** — removed `legend_blocked`, `legend_done`, `legend_normal`; `auto_validation_kanban_state` renamed to `auto_validation_state`.

**project.task.recurrence** — removed `task_template_id`, `next_recurrence_date`, `recurrence_left`, `repeat_number`, `repeat_on_month`, `repeat_on_year`, `repeat_day/week/weekday/month` and the weekday booleans; `repeat_type` keeps only `forever` and `until`.

**Analytic accounting** — the `analytic_plan_id` field on `res.company` is removed; the plan used for project accounts is now configured globally, and analytic data can be compared across plans.

## How your habits should change

- Stop using the green/red kanban dots: set the task State instead. "Ready" and "Blocked" no longer exist as such; a blocked task shows Waiting.
- A task is no longer closed by reaching a folded stage: only the state closes it, so mark tasks done explicitly.
- When setting up a recurrence, choose an interval, a unit and an end (forever or a date). Repeating on the first Monday of the month, or a fixed number of repetitions, is not supported anymore.
- Deleting the last task of a recurrence deletes the whole recurrence.
- If you import/export tasks, use `project_id`; `display_project_id` no longer exists and a task is private when it has neither a project nor a parent.
- Sub-tasks are always available: the "Sub-tasks" setting is gone.

## What you gain by migrating

A single explicit task status that drives closing, dependencies, milestones and reporting; recurring tasks created when the previous one is finished, so field work can be anticipated; cleaner task data with fewer confusing fields to import or report on; and analytic accounting that can mix several plans. The upgrade also removes the legacy Kanban-state legends and the recurrence scheduler, simplifying daily work for project managers and giving more reliable project statistics.
