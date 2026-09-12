# sales_team migration guide (12.0 -> 13.0)

`sales_team` is the shared module that defines sales teams (`crm.team`) and their dashboard. Sales, CRM and Point of Sale all build on it, so these changes affect every team you set up in 12.0.

## What's new for users

The 13.0 release notes mention this addon in one line only, under CRM → Sales Teams: **"Simplified sales team configuration."** Nothing else there concerns the Community `sales_team` module, so the rest of this guide comes from the code changes themselves.

* **Sales teams are no longer typed.** The "Team Type" choice (Sales / Website) is gone; a team can now handle quotations, leads, POS or website orders without being declared as a specific kind.
* **The team form is shorter.** The dashboard graph options (graph type, content, group by, scale) have been removed from the configuration.
* **Dashboard graphs are uniform.** Every team now shows the same graph: the last month, grouped by week.
* **Members are clearer.** The Members field accepts internal users only and explains that a user belongs to a single team, which receives their documents automatically.
* **Default teams are protected.** The website sales, POS and eBay teams can no longer be deleted by mistake.

## Technical data model changes

On `crm.team`:

* **Fields removed (5):** `team_type`, `dashboard_graph_type`, `dashboard_graph_model`, `dashboard_graph_group`, `dashboard_graph_period`. No field was added.
* **Field changed:** `member_ids` now carries a domain restricting it to internal users (`base.group_user`) and a help text about single-team membership.
* **Method removed:** `_onchange_team_type`, useless without `team_type`.
* **Method added:** `_graph_get_model()`, a skeleton that raises a `UserError` when the concrete module (Sales or CRM) does not define its graph model — previously read from `dashboard_graph_model`.
* **Dashboard code simplified:** `_compute_dashboard_graph`, `_graph_get_dates` and `_graph_x_query` are hard-coded to one month grouped by weeks (`EXTRACT(WEEK FROM create_date)`); the day/week/month/salesperson branches are gone.
* **`_get_default_team_id`:** the `use_leads` condition was dropped; only `active` is tested now.
* **New `unlink()` guard:** the default teams `salesteam_website_sales`, `pos_sales_team` and `ebay_sales_team` can no longer be deleted.

On `res.users`:

* The `create()` override that silently put a new salesperson into the only team of type "Sales" was removed. `sale_team_id` itself is unchanged.

## How your habits should change

* **No more team type** when creating a team — the question no longer exists, and filters or reports built on `team_type` must be reworked.
* **No more graph tuning per team.** If you used "Group by: Salesperson" or a yearly scale, that option is gone; every dashboard is monthly, week by week.
* **Assign new salespeople manually.** They are no longer auto-added to a team, even if only one exists.
* **Keep the one-team rule in mind** when adding members, since membership drives document assignment.
* **Leave the POS, website and eBay default teams alone** — Odoo now blocks their deletion.

## What you gain by migrating

* **A much lighter team form**, with the fields that brought no added value removed — less training, fewer mistakes.
* **Comparable dashboards** across teams, instead of apples-to-oranges graphs caused by per-team settings.
* **Teams that are not boxed in:** one team can follow quotations, leads, POS and website orders.
* **Safety nets:** protected default teams and a documented, single-team membership rule.
* **A clean upgrade path** to Odoo 13.0 for Sales, CRM and Point of Sale, which all depend on this module, together with 13.0 usability improvements such as the cleaner sales order form and unified order/confirmation dates.
