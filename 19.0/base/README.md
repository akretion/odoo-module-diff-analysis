# base migration guide (18.0 -> 19.0)

## What's new for users

**Users and groups rebuilt.** The user form no longer uses "reified" group fields; rights are shown through the group hierarchy, and `res.groups` gains its own Users view. Users are no longer automatically added to every implied group — implied rights are computed. Odoo also now enforces that at least one administrator user always remains.

**API keys on the user form.** API keys are now editable directly on the user record (own keys are self-readable/self-writable), with a "New API Key" wizard.

**Server actions got a real workspace.** Kanban navigation between parent and child actions, auto-generated names, a "duplicate record" action type, a warning panel, and a code history with diff and restore.

**Everyday wins.** Bank BIC is stored uppercase; banks can be archived in Contacts; module install no longer offers Cancel buttons (a "reset state" action replaces them); invalid view locators are highlighted.

**Platform improvements (Community).** Data and translations fetched while browsing are cached, and the control panel renders before data loads, so navigation feels faster. Dates display in abbreviated form (Aug 1, 2025). List-view mass edits accept `+= -= *= /=`. Binary fields accept any file format on import. When a record's parent changes, property values are logged in the chatter.

## Technical data model changes

- `res.users` absorbs `api_key_ids`; helper classes (`UsersImplied`, `APIKeysUser`) and reified group fields are gone. New fields/checks: `all_group_ids`, `role`, `_check_at_least_one_administrator`, disjoint-group checks, session-token helpers.
- `res.groups`: new `all_implied_ids`, `all_user_ids`, `all_users_count`, group-hierarchy compute, `action_show_all_users`, `_is_feature_enabled`; removed `get_application_groups`, `get_groups_by_application`.
- Server actions: new `ir.actions.server.history` model and wizard, `_can_execute_action_on_records`, `_check_children`, `_compute_allowed_states`, `action_open_parent_action`, duplicate-record action.
- ORM signature cleanups: `write(vals)`, `name_search(domain=)`, `default_get(fields)`, `_search(..., active_test, bypass_access)`, `models.Index`/`UniqueIndex` replacing many `_auto_init` calls.
- Removals: `ir.sequence.get()`/`get_id()`, `res.company._company_default_get()`, gravatar images on partners, module cancel buttons, `toggle_active`.
- `ir.cron` methods become static functions (`_process_job(cron_cr, job)`, `_process_jobs(db_name)`), plus new `_commit_progress`/`_clear_schedule`.
- QWeb/assets: parametric `t-call`, `t-cache` directive removed, batched template preloading, new `autoprefix` flag on asset bundles (`t-autoprefix`).
- Attachments: `_check_access(operation)`, `_file_read(fname, size=None)`, raw access tokens.

## How your habits should change

- Review your groups if you relied on implied groups: users are no longer physically linked to every implied group.
- Re-check customizations using removed helpers (`ir.sequence.get`, `_company_default_get`, gravatar, module cancel buttons, `toggle_active`).
- Update argument names in your own overrides (`vals`, `domain`, `field_name`): Odoo now checks override signatures.
- Re-test custom views after the users/groups and server-action UI changes, and review your automated actions (validation is softer and warning-based).
- Enjoy caching, but re-validate templates that used `t-cache`.

## What you gain by migrating

- Clearer user and access-rights management.
- A modern server-action editor with history and rollback — no more lost Python code.
- Faster UI (cached data, pre-rendered control panel) and cleaner dates and mass edits.
- Better attachment access control and batched QWeb rendering.
- A supported platform: 18.0 is now in maintenance while 19.0 receives the new features.
