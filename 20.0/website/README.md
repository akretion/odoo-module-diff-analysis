# website migration guide (19.0 -> 20.0)

Scope: Odoo Community 19.0 → 20.0, `website` addon, for functional users and project owners. No official release-note extract covers this addon, so this summary is built from the 19.0 → 20.0 source diff.

## What's new for users

**Publication scheduling reworked.** Pages, blog posts, products and events now share one scheduling mechanism. The old "Publishing Date" is replaced by two fields: *Auto publish on* (plan a future publication) and *Published date* (when it actually went live). A scheduled action publishes content when the date arrives, and an *Unschedule* action cancels the plan. Scheduled content is no longer announced to followers, nor listed in `sitemap.xml` while still offline.

**Social media snippets are independent.** Editing the links of one snippet no longer changes the others. Links are pre-filled from the company social accounts, and the sidebar offers *Replace Icon* (a pencil icon is used when no icon matches).

**SEO.** A structured-data layer now emits Schema.org JSON-LD (Organization, BreadcrumbList, Product, Event, BlogPosting…) and web pages can show a breadcrumb trail. `llms.txt` can be generated and managed from the SEO settings.

**Everyday editing.** Static page URLs can be translated per language; website pages use a new high-level page cache and better font loading; the website logo is available from any media manager. Third-party tracker blocking (e.g. Google Ads) and cookie-policy page handling were fixed. The configurator, the site search (missing-word tolerance, term highlighting) and `sitemap.xml` (reachable URLs only) were improved, and the first website is now treated as the default one.

## Technical data model changes

- `website.published.mixin` (all inheriting models): added `publish_on` and `published_date` (Datetimes); new hooks `_check_for_action_post_publish`, `_finalize_publication`, `action_unschedule`, `_cron_publish_scheduled_pages`, `_manage_next_scheduled_action`.
- `website.page`: `date_publish` removed; visibility now depends only on the published flag. `website.page.properties` exposes the new `publish_on` / `published_date`.
- `website`: the eight `social_*` fields are removed; `name`, `sequence`, `domain`, `domain_punycode`, `company_id`, `user_id` are now defined by a skeleton model in `base` (XML ID `base.default_website`). `get_current_website()` is removed: the current website comes from the context (`website_id` / `host_id`).
- New mixin `website.structured_data.mixin` (`_prepare_jsonld_vals`, `_get_jsonld_dict`, `_render_jsonld`, breadcrumb helpers).
- Signature changes: `_get_default_lang`, `_render_template`, `_get_error_html`, `_is_allowed_cookie` are no longer classmethods; `_auth_method_public(cls, routing)`; `_search_exact` / `_search_with_fuzzy` / `_search_fetch` take an `offset`; `website.visitor._upsert_visitor` changed and `_get_visitor_from_request` moved to `ir.http`; `website.route` uses `name_search`; `website.technical.page` uses `_table_sql`; view-saving helpers (oe-structures, custom snippets) moved to `website`; access rights use the new `ir.access` model.

## How your habits should change

- Stop using the single "Publishing Date": use *Auto publish on* to schedule, *Published date* as a trace, *Unschedule* to cancel. Review pending scheduled content before go-live.
- Configure social accounts on the company or directly in each snippet, not in website settings, and verify every social snippet once.
- URLs being translatable, review language-specific URLs and old redirects after migration.
- With several websites, remember the current website is resolved from the context and the first website is the default.
- Any customisation or third-party module relying on removed helpers (`get_current_website`, `request.website`, `date_publish`, `social_*` on `website`) must be adapted.

## What you gain by migrating

Content planning becomes reliable: scheduled pages, posts and products go online exactly when planned, without early notifications or false sitemap entries. Marketing gains per-language URLs, richer SEO metadata (JSON-LD, breadcrumbs, `llms.txt`) and independent social snippets. Visitors get faster pages and better search, while stronger tracker blocking lowers compliance risk. Technically, the website model moves into `base` and the current website into the request context, making multi-website deployments cleaner — migrating now avoids drifting further from a consistent, supported codebase.
