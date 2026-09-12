# analytic migration guide (15.0 -> 16.0)

This addon carries Odoo's "Analytic Apocalypse" refactoring (PR #98914). Analytic **tags**, **groups** and the old **distribution** records disappear and are replaced by **plans**, **applicability rules** and **distribution models**. The change is deep: plan for a real data migration of your analytic tags.

## What's new for users

- A new **analytic distribution widget** on invoices and other documents supporting analytics: you set the percentage per analytic account in one field instead of pairing an account with tags.
- **Analytic plans** replace analytic groups. Plans are hierarchical, and every analytic account belongs to one plan.
- **Analytic distribution models** replace the old default-distribution rules: they prefill the distribution automatically from the partner, the partner category or the company.
- **Applicability** per plan defines when a plan is offered in the widget (Optional, Mandatory, Unavailable), with rules per business domain (Sales, Purchase, Miscellaneous).
- Analytics can be **edited in mass from list views**.
- Note: breaking financial reports down by analytic account/plan is an Enterprise feature and is **not** included in this Community addon.

## Technical data model changes

**Removed models**
- `account.analytic.tag`
- `account.analytic.distribution` (fields `percentage`, `tag_id`, `account_id`)
- `account.analytic.group`

**Added models**
- `account.analytic.plan`: `name`, `description`, `parent_id`/`parent_path`/`children_ids`, `complete_name`, `company_id`, `account_ids`, `color`, `default_applicability`, `applicability_ids`.
- `account.analytic.applicability`: `analytic_plan_id`, `business_domain`, `applicability`.
- `account.analytic.distribution.model`: `partner_id`, `partner_category_id`, `company_id`, plus an analytic distribution and a `_get_distribution()` helper used to prefill documents.
- `analytic.mixin` (abstract): `analytic_distribution` (binary) and stored `analytic_distribution_stored_char` (JSON), shared by every model carrying a distribution.

**`account.analytic.account`**
- New required `plan_id` (check_company), stored computed `root_plan_id`, `color` related to the plan.
- `group_id` removed.
- `active` and `code` are now tracked; `currency_id` is no longer forced read-only.
- `name_get` renders `[code] name - customer`.

**`account.analytic.line`**
- `tag_ids` and `group_id` removed; new stored related `plan_id` (from `account_id.plan_id`).
- Core fields unchanged: `name`, `date`, `amount`, `unit_amount`, `product_uom_id`, `account_id`, `partner_id`, `user_id`, `company_id`, `currency_id`, `category`.

**Behaviours**
- A constraint blocks changing the company of an analytic account that already has analytic lines.
- The balance computation no longer accepts a `tag_ids` context filter (tags are gone).
- No method signature changes were detected in this addon.

## How your habits should change

- Stop thinking in tags: distribution tags (with percentages) and purely informative tags both disappear. Put the split on accounts and use plans to categorise.
- Re-create your analytic **groups as plans**; every account needs one.
- Instead of tagging a line, open the analytic widget and distribute the amount across accounts/plans.
- Replace default distribution rules with **distribution models** (partner / partner category / company).
- Use **applicability** per plan to hide irrelevant plans on sales, purchase or miscellaneous documents.
- Any filtering or reporting done on tags must be rebuilt on accounts or plans.

## What you gain by migrating

- One consistent entry point for analytics on invoices and other documents, removing the tag/account confusion.
- Hierarchical plans with applicability and colours: a clearer structure for your analytic chart.
- Automatic prefill through distribution models, reducing manual entry and mistakes.
- Mass edition of analytics directly from list views.
- A single shared `analytic.mixin`, so custom models can reuse the same widget and reporting.
