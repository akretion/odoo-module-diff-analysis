# digest migration guide (13.0 -> 14.0)

## What's new for users

The Odoo 14.0 release notes mention this addon in a single line: *"A digest containing a tip is sent to users on a periodic basis."* Everything else is technical. Concretely, for someone receiving digests:

- **Tips are now part of the digest email.** The email includes a "did you know?" tip, so it advertises other apps and good practices instead of only showing figures.
- **A refreshed, more readable layout.** KPIs are displayed in three columns — *Yesterday*, *Last 7 Days*, *Last 30 Days* — each with the value and its margin versus the previous equivalent period.
- **KPIs can link to an action.** Clicking a KPI jumps straight to the relevant records (for example the CRM pipeline).
- **Emails are sent in the recipient's language**, not the sender's.
- **Preference links at the bottom of the email.** Managers see "Switch to weekly Digests" and "Choose the metrics you care about". If you stop connecting for a few days, the email explains that your preference was automatically switched to weekly.
- **Tip tracking fixed.** A bug flagged tips as sent to the wrong records, so users could see the same tip again. This is corrected.

The other Usability items of the 14.0 release notes (Gantt, map grouping, grid view, new listview, etc.) concern other addons or Enterprise-only features and are not part of this addon.

## Technical data model changes

- **Removed field:** `template_id` (Many2one to `mail.template`, required) on `digest.digest`. No new field was added.
- Rendering switched from `mail.template.send_mail()` to QWeb: `mail.render.mixin._render_template()` (engine `qweb`, `post_process=True`) renders the body view `digest.digest_mail_main`, then `_render_encapsulate()` wraps it with `digest.digest_mail_layout`. A `mail.mail` record is created (`auto_delete=True`) and sent, without attachments.
- Subject is unchanged: `"<Company name>: <Digest name>"`. The sender is now the company's email address; the recipient is the user's formatted email.
- `compute_kpis(company, user)` return value changed shape: from a dict keyed by timeframe (`yesterday` / `lastweek` / `lastmonth`) to a **list** of KPI dicts holding `kpi_name`, `kpi_fullname`, `kpi_action` and `kpi_col1` / `kpi_col2` / `kpi_col3`, each with `value`, `margin` and `col_subtitle`. KPIs the user cannot read are filtered out.
- `_compute_timeframes()` now returns an ordered list of `(translated label, (current period, previous period))` tuples instead of a dict.
- `compute_kpis_actions()` was renamed to `_compute_kpis_actions()` (now private); `compute_preferences()` now returns a list of HTML snippets instead of a rendered QWeb string.
- New helper `_get_kpi_fields()` returns the selected `kpi_*` / `x_kpi_*` / `x_studio_kpi_*` boolean field names.
- Bug fix in `compute_tips()`: `tip.user_ids` corrected to `tips.user_ids`.

## How your habits should change

- **The digest email can no longer be edited from Email Templates.** In 13.0 the design came from the `digest.digest_mail_template` record. In 14.0 that record is removed by the upgrade and replaced by QWeb views. To customise the design, a developer must edit or inherit `digest.digest_mail_main` and `digest.digest_mail_layout` (Settings > Technical > User Interface > Views, or a custom module).
- **One design applies to all digests**: picking a different template per digest is no longer supported.
- **Selecting metrics is unchanged**: tick the KPI booleans on the digest form; they are simply rendered as a three-column table.
- Daily routine is otherwise the same: digests are still sent per user according to the periodicity, and each user only sees data they are allowed to read.

## What you gain by migrating

- A more attractive and consistent digest email, focusing on adoption and engagement.
- Multilingual delivery: every recipient gets the digest in their own language, with no extra configuration.
- A clear three-column comparison of each KPI with its margin, plus direct links to the related actions.
- Tips that reliably reach users once, and a built-in way for managers to switch to weekly or choose their metrics.
- Less maintenance: no mail template record to keep in sync, and the standard upgrade script cleans up the former field.
