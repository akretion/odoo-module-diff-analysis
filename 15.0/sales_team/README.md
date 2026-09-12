# sales_team migration guide (14.0 -> 15.0)

## What's new for users

The official 15.0 release notes mention this addon in a single line, under CRM > Sales Teams: **"Assign a salesperson to multiple teams."** The rest of that section (predictive lead scoring, dispatch/assignment rules, Gmail/Outlook connectors) belongs to other modules or to Enterprise and is *not* part of this Community addon.

Concretely: in 14.0 a salesperson could only be a member of one sales team in the base model — multi-team membership lived in the Enterprise scoring app (`website_crm_score`). In 15.0 the membership model is moved into `sales_team` itself, so Community users get multi-team membership, with a setting to switch between mono-team and multi-team mode.

## Technical data model changes

**New model: `crm.team.member`** (Sales Team Member, mail.thread mixin, ordered by creation date). Fields: `crm_team_id`, `user_id` (indexed, `ondelete=cascade`), `active`, `user_in_teams_ids` (UX helper listing users already in the team), plus related salesman info: `image_1920`/`image_128`, `name`, `email`, `phone`, `mobile`, `company_id`.

**`crm.team`**
- `member_ids` changed from a `One2many` on `res.users.sale_team_id` to a computed/inverse/search `Many2many` "Salespersons", backed by `crm.team.member`.
- `crm_team_member_ids`: `One2many` to `crm.team.member` ("Sales Team Members").
- New `crm_team_member_all_ids`: same, including inactive memberships.
- New computed fields `is_membership_multi` and `member_warning`.
- `_get_default_team_id()` now resolves the team through `member_ids` (signature now defaults `user_id` to the current user); the previous `lead_all_assigned_month_count` computation is replaced by a sum of the leads assigned to the team's members.

**`res.users`**
- New `crm_team_ids` (computed, read-only `Many2many` to `crm.team`) and `crm_team_member_ids` (`One2many`).
- `sale_team_id` is no longer an inverse field: it is now a **computed stored** `Many2one` holding the user's "main team", i.e. the *oldest active membership* (memberships sorted by creation date). It is read-only and used for pipeline, invoicing and subscriptions.

**Constraints and behaviour**
- The SQL unique constraint on (`crm_team_id`, `user_id`) is replaced by a Python constraint that only applies to *active* memberships, so archived memberships of the same pair can coexist.
- `create()` / `write()` in mono-membership mode archive the user's other memberships automatically (`_synchronize_memberships`).
- New system parameter `sales_team.membership_multi` toggles mono/multi mode.

No public method signature changes are reported for this addon beyond the internal default-team helper.

## How your habits should change

- Adding a member to a sales team now creates a membership record. In the default **mono-team mode**, adding someone who already belongs to another team archives that previous membership (history is kept, nothing is deleted).
- A warning is displayed on the team form in that case, with a hint to enable the Multi-Teams option in the CRM settings.
- **Sales Team > Members** becomes the place to review, archive or re-activate memberships, including archived ones.
- `User's Sales Team` on the user form is now read-only and always reflects the *first* active membership. To change a user's main team, change the memberships, not the field.
- Editing `user_id` or `crm_team_id` directly on an existing membership is not supported: create or remove memberships instead.
- Automatic lead assignment and its limits now live in the `crm` module; only the membership foundation stays here.

## What you gain by migrating

- **Multi-team membership in Community**, previously an Enterprise-only capability.
- **No disruption for simple setups**: mono-team remains the default behaviour, and the multi-team switch is explicit.
- **Traceability**: memberships are archived rather than lost, so you keep the history of team changes.
- **Cleaner rules**: a proper membership object (with salesman info directly on it) is the foundation for lead assignment, pipeline and per-team reporting in 15.0.
