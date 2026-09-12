# hr_timesheet migration guide (13.0 -> 14.0)

Scope: the Community addon `hr_timesheet` as shipped in Odoo 14.0. Audience: functional users and customers planning a 13.0 -> 14.0 upgrade.

## What's new for users

The official 14.0 release notes for Timesheets are written largely for the Enterprise Timesheet app (grid view, barchart footer, avatars, timer), so only a few announced items concern your Community addon:

- Optional fields can be hidden or displayed in the list view, so each user keeps only the columns they care about.
- Timesheets can be validated in batches from the list view, and are automatically validated when you click the corresponding button.
- While encoding timesheets, the remaining hours are shown in the task name.
- Several timesheet entries can be merged into one.
- The onboarding tour was improved.
- On the portal, durations are displayed in days when the timesheet was encoded in days.

The release notes also advertise a timer (hot keys, "add 15 minutes", timers on tasks and tickets). Be aware: that timer is part of the Enterprise offering. In Community 14.0 the timer code is no longer part of `hr_timesheet` (see below). Do not expect it after migrating to Community.

## Technical data model changes

The 13.0 -> 14.0 diff for `hr_timesheet` is mostly a removal. During the 14.0 development cycle the timer was first rewritten (new `timer.mixin` abstract model, `display_timer` on `account.analytic.line`, `allow_timesheet_timer` on `project.project`) and then moved to the Enterprise repository before release. Net effect on Community 14.0:

Removed from `account.analytic.line`:
- `display_timer` (previously showed the timer button when the encoding unit is Hours)
- timer methods `action_timer_start`, `action_timer_stop`, `_stop_running_timers`
- the `unlink()` override that also deleted related `timer.timer` records

Removed from `project.task`:
- `timer_start`, `timer_pause`
- `timesheet_timer_first_start`, `timesheet_timer_last_stop`
- `display_timesheet_timer`, `display_timer_start_secondary` and the other `display_timer_*` computed booleans
- `action_timer_start`, `action_timer_stop`, `_action_create_timesheet`

Removed from `project.project`:
- `allow_timesheet_timer` and its SQL constraint `timer_only_when_timesheet`
- the `write()` side effect that reset running timers when the option was switched off

Removed model: `timer.mixin` (abstract), which existed only between two development commits; it is not in 14.0.

Changed:
- `account.analytic.line.write()` still forbids non-approvers from writing other people's timesheets, but the check is now a single batch test and the message is "You cannot access timesheets that are not yours."
- No method signature change was reported for this addon.

In short: no new model or field to configure in Community; the data model gets smaller.

## How your habits should change

- If your team used the 13.0 task/timesheet timer, it disappears: with Community 14.0 you encode time by typing the hours/duration in the list, kanban or grid view. Keeping the timer means moving to Enterprise.
- Before upgrading, stop any running timer and check that the time it would have created is already encoded; the timer fields do not survive the migration.
- Work more from the list view: hide unneeded optional columns, select many lines and validate them in one action instead of line by line.
- While encoding, read the task name: remaining hours are displayed there, so you no longer need to open the task to check the budget.
- If a user needs to edit someone else's timesheet, remember that only approvers can do so; the wording of the refusal message has changed.

## What you gain by migrating

- A lighter, faster timesheet screen: fewer technical fields, optional columns, batch validation.
- Clearer error messages with unchanged access rules: only approvers edit other people's timesheets.
- A cleaner data model with no leftover timer fields, hence no stale "timer still running" states after the upgrade.
- Better visibility where you need it: remaining hours on task names while encoding, durations in the unit you actually use on the portal.
- A code base aligned with the Enterprise timer implementation, making a later Enterprise upgrade straightforward if the timer becomes a requirement.
