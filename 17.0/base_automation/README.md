# base_automation migration guide (16.0 -> 17.0)

## What's new for users

Automated Actions have been rebuilt around a clearer, more discoverable workflow. The feature is now called **Automation Rules**, and the related server action type is labelled "Automation Rule" as well.

- **Reach automations from any Kanban view**: a new entry in the Kanban header configuration dropdown lets you create and edit the automation rules of the model you are looking at.
- **A dedicated Kanban view** shows each rule at a glance together with the actions it runs.
- **Smart triggers** appear depending on the model you select, so you pick from meaningful choices instead of raw technical ones:
  - *Updated values*: "Stage is set to", "State is set to", "Priority is set to", "User is set", "Tag is added", "On archived", "On unarchived".
  - *Timing*: "After creation", "After last update".
- **Several actions per rule**: a rule can now contain multiple server actions directly, so the old "Execute several actions" action type is no longer needed.
- **Simpler server actions**: "Update the record" actions now write to a single field, and the value widget adapts to the field being updated. The "Create" action now works as a name creation action.

The Odoo 17 release notes also advertise webhooks and Studio-driven rule editing; those belong to the Enterprise/Studio offering and are **not** part of this Community addon. Everything described above comes from the Community module itself.

## Technical data model changes

- `base.automation.action_server_id` (Many2one, delegated) is replaced by `action_server_ids` (One2many on `ir.actions.server.base_automation_id`). `ir.actions.server` gains the `base_automation_id` field, and `name` / `model_id` now live on `base.automation`.
- The `trigger` selection is rewritten and computed/stored. It gains the smart triggers plus `on_time_created` and `on_time_updated`; `on_create` and `on_write` are kept but marked deprecated.
- New fields: `trg_selection_field_id`, `trg_field_ref`, `trg_field_ref_model_name`, `trg_field_ref_display_name`.
- `trigger_field_ids` is now computed from the selected trigger and model, and `filter_domain` becomes computed/stored too.
- `ir.server.object.lines` and its `fields_lines` One2many are removed; their attributes are merged into `ir.actions.server`.
- Renamed internals (`_add_postmortem`, `_compute_trigger_and_trigger_field_ids`) plus the `date_action_last` → `date_automation_last` field tracked by time-based rules. The `usage` label changes from "Automated Action" to "Automation Rule".
- Validation moved from `state` to `action_server_ids`; "On live update" rules still only accept Execute Python Code actions, and deletion rules still reject email/follower/activity actions.

## How your habits should change

- Create rules from the Kanban configuration menu instead of hunting for the Automated Actions menu.
- Stop using a single "Execute several actions" action: list your actions directly in the rule.
- Prefer "On save" over the deprecated "On creation" / "On update" triggers when editing existing rules.
- For "Update the record" actions, remember that one action writes one field; create one action per field.
- Review archive/unarchive, state, stage, priority, user and tag rules after migration: their filtering is now expressed through the new computed triggers.

## What you gain by migrating

Migration brings automation configuration within reach of functional users: triggers are expressed in business language, several actions live in one rule, and rules are visible and editable right from the Kanban view of the model they affect. Server actions become easier to configure and less error-prone thanks to the adaptive value widget. The result is fewer clicks, less reliance on technical action types, and automation rules that are easier to audit and maintain.
