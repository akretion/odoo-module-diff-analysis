# hr_timesheet migration guide (16.0 -> 17.0)

## What's new for users

The 17.0 release notes for Timesheets list four highlights, but none of them ships in the
Community `hr_timesheet` addon:

- *Awesome Timesheet deprecation*, the *billing rate leaderboard* and the
  *Grid view: overtime / missing hours* indicator belong to the Enterprise Timesheets app
  (`timesheet_grid`). Do not expect them from a Community migration.
- *Bill timesheets from projects without a sales order* concerns `sale_timesheet` and
  invoicing flows, not this addon.

In short: for `hr_timesheet` itself, 17.0 brings no new user-facing screen or menu. What a
Community user will actually notice comes from the task/project clean-up described below.

## Technical data model changes

**1. Files split per model (PR #116256).** The addon code was reorganised so that each file
holds only the fields and methods of one model (`project_project.py`, `project_task.py`).
Most "removed" declarations in the diff are duplicates now living in the file of their own
model; this has no impact on your database.

**2. `project_root_id` disappears from `project.task` (PR #128281).** In 16.0, a sub-task whose
project was cleared kept `project_id = False` and found its project back through
`project_root_id`. In 17.0:

- every non-private task keeps a real `project_id`;
- `allow_timesheets` is now related to `project_id.allow_timesheets` (was `project_root_id`);
- `analytic_account_active` depends on `project_id.analytic_account_id.active`;
- the `task_id` domain on timesheets and the timesheet `project_id` computation use
  `project_id` only;
- both related fields became `recursive=True`, so sub-tasks keep following their parent chain;
- whether a sub-task is *listed* inside its project is now driven by a technical boolean
  `display_in_project` (provided by the `project` module), not by an empty `project_id`.

**3. New blocking constraint.** `_check_project_root` raises a `UserError` — "This task cannot
be private because there are some timesheets linked to it" — when you remove the project of a
task that already has timesheets. It replaces the old `Task.write()` override that raised
"This task must be part of a project...". The `display_project_id` domain override declared in
this addon was also dropped (the field itself comes from `project`). Unchanged: unlinking a
project or task that has timesheets still shows a RedirectWarning offering to view the entries.

No other method signature change was detected (`method_signatures: NO`).

## How your habits should change

- **Do not clear the project of a task that already has timesheets**: the action is now refused.
  Reassign or delete the timesheet lines first.
- **Hide a sub-task instead of detaching it.** Clearing the project in the form no longer
  removes the link in the database; the task keeps the project's timesheet settings and analytic
  account. That is exactly what lets you log time on a sub-task without cluttering the project's
  task list.
- **Review your filters and reports.** Domains, group-bys and exports on `project_id` now
  include hidden sub-tasks. If you used `project_id = False` to isolate them, filter on
  `display_in_project` instead.
- **Check your customisations.** Any custom field, server action, automated action or report
  referencing `project_root_id` must be rewritten to use `project_id` before the migration.

## What you gain by migrating

- **One clear notion of "project" on tasks**, instead of two overlapping fields; timesheet
  settings (allow timesheets, analytic account) propagate reliably from a project to all its
  sub-tasks.
- **Safer data**: timesheets can no longer sit on private tasks, and the analytic account chain
  stays consistent, which protects your timesheet and analytic reporting.
- **A lightweight migration**: no new field to populate and no data loss in this addon; the only
  real work is the `project_root_id` removal in your customisations.
- **A cleaner code base** (one file per model), easier to maintain and extend.
- **A smoother path beyond 17.0**, including a later move to the Enterprise Timesheets app if
  you need the leaderboard, billing-rate or overtime-grid features.
