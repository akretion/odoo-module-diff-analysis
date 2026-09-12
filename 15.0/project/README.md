# project migration guide (14.0 -> 15.0)

This guide covers the `project` addon in the Community edition.

## What's new for users

- **Multiple assignees.** A task can be assigned to several users; the single "Assigned to" field is gone.
- **Personal stages (GTD).** Each assignee can file their tasks in their own pipeline (Inbox, Today, This Week, This Month, Later, Done, Canceled), independent of the project's stages.
- **Private tasks.** A task with no project is private: only its assignees can see it.
- **Task dependencies.** A task can be "Blocked by" others (Settings ▸ Task Dependencies). Assignee changes, blocked-by changes, and stage/deadline changes of dependent tasks are logged in the chatter.
- **"My Tasks"** action now opens in kanban by default.
- Portal project sharing: Community only ships the collaborator data model; the sharing screens and portal edit rights remain Enterprise-only.

Gantt, burndown/profitability and upsell items in the 15.0 release notes belong to Enterprise or other apps, not to this addon.

## Technical data model changes

**New models**: `project.task.stage.personal` (table `project_task_user_rel`: task_id, user_id, stage_id, one stage per user and task) and `project.collaborator` (project_id, partner_id).

**Removed model**: `project.sharing.access` (project_id, user_id, access_mode read/comment/edit).

**project.project**: added `allow_task_dependencies`, `task_count_with_subtasks`, `collaborator_ids`, `collaborator_count`; removed `subtask_project_id`, `allowed_user_ids`, `allowed_internal_user_ids`, `allowed_portal_user_ids`, `project_sharing_access_ids`. `task_count` now ignores sub-tasks.

**project.task**: added `depend_on_ids` ("Blocked By"), related `allow_task_dependencies`, `display_project_id`, `child_text`, `is_private`, `user_ids` (many2many), `personal_stage_type_ids`, `personal_stage_id`, `personal_stage_type_id`; removed `user_id`, `user_email`, `ribbon_message`, `subtask_project_id`, `allowed_user_ids`, `project_sharing_access_mode`. `project_id` is no longer required. New constraints: no cyclic dependencies, and a sub-task cannot be recurrent.

**Others**: `project.task.type` gains `user_id` ("Stage Owner") for personal stages; visibility labels become "Invited employees / All employees / Portal users and all employees" and are enforced through followers/collaborators rather than allowed-user lists; `priority` is now tracked; `message_subscribe`/`message_unsubscribe` lose their `channel_ids` argument; `res.partner` gains `_create_portal_users()`.

## How your habits should change

- Assigning is now plural: pick one or several assignees. A sub-task still inherits its parent's assignee by default; recurring tasks are created unassigned.
- Project stages stay shared for everyone; each user also drifts their tasks through their own personal stages in "My Tasks".
- Sub-tasks have no project of their own any more: they live in the parent's project and are hidden from project task lists and counts (use the "with sub-tasks" counter and the parent task to find them).
- Access control: forget the "Visible to" user lists — access comes from project followers and, for portal users, from collaborators. Moving a project away from portal visibility unsubscribes portal followers from the project and its tasks.
- Enable Task Dependencies (globally or per project) before using "Blocked by"; cyclic dependencies are refused, and a sub-task cannot be recurring.

## What you gain by migrating

- Teamwork on a single task: several assignees, each with their own GTD view of what to do next.
- A clear answer to "what can I start now?" with enforced task dependencies and chatter follow-up.
- Private tasks for personal to-dos, without creating a project.
- Cleaner project counters: sub-tasks no longer inflate task counts and always stay in the right project.
- Simpler privacy: followers and collaborators instead of parallel allowed-user lists, so fewer mistakes in granting access.
