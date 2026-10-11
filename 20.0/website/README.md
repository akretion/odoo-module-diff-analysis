# website migration guide (19.0 -> 20.0)

Changes in this addon between 19.0 and 20.0 focus on **scheduled publishing**, **structured data (SEO)** and **social media snippets**. Enterprise-only 20.0 announcements are not listed.

## What's new for users

**Publication scheduling.** Scheduling (blog post, product, event, static page) is now a first-class feature: content stays offline until its scheduled date, then is published automatically. Followers are notified only when the page really goes live, and search engines no longer index pages that are not yet available.

**Structured data (JSON-LD).** Every page renders Schema.org JSON-LD: an Organization block for the site plus page-type blocks (blog posts, products, events, jobs, forum Q&A, courses...) and breadcrumb trails, to obtain Google Rich Results.

**Breadcrumbs on static pages.**

**Social media links.** Social links are now handled uniformly in the "Social Media" inner content block, and the "Social Media" / "Share" blocks have been simplified. On Blog, Events and Jobs pages with a sidebar, "Follow Us" and "Share" are replaced by standard Social Media and Share snippets. Links are prefilled from the company's social accounts.

**Sitemap and SEO.** Sitemaps only include indexable pages that actually respond (2XX) and stay within Google's size limits. Mobile SEO is improved with adaptive image sizing and refined duplicate-content handling on paginated pages.

## Technical data model changes

`website.published.mixin` (web pages, blog posts, products, events...):
- **Added** `publish_on` ("Auto publish on") and `published_date` ("Published date"), both Datetime.
- New methods: `action_unschedule()`, `_finalize_publication()`, `_check_for_action_post_publish()` (subclass hook), `_cron_publish_scheduled_pages()`, `_manage_next_scheduled_action()`.
- `create()`, `write()` and `website_publish_button()` now clear `publish_on` on publishing, and force `is_published = False` when a record is scheduled or archived.

`website.page`: `date_publish` **removed**; `_compute_visible` now only depends on `website_published`.
`website.page.properties` (editor panel): `date_publish` **removed**; `publish_on` and `published_date` **added** (with inverses), plus `action_unschedule()`.
The website sitemap domain no longer filters on `date_publish`.

**New abstract model** `website.structured_data.mixin` (`_prepare_jsonld_vals`, `_get_jsonld_dict`, `_render_jsonld`, `_get_breadcrumb_items`, `_build_breadcrumb_jsonld`, `_build_collectionpage_jsonld_vals`, `_build_postaladdress_jsonld_vals`, `_to_iso_datetime`). `website` inherits it and supplies the Organization schema.

`website`: the eight social account fields (`social_facebook`, `social_twitter`, `social_github`, `social_linkedin`, `social_youtube`, `social_instagram`, `social_tiktok`, `social_discord`) are **removed**. A minimal `website` skeleton (domain, company, public user) now lives in `base`, the current website is available on the environment, and the default website XML ID becomes `base.default_website`.

## How your habits should change

- Schedule content with the new **"Auto publish on"** option instead of a publishing date. Setting it unpublishes the record; publishing manually cancels the schedule.
- Scheduled publishing depends on the **"Website Publish Mixin: Publish scheduled website page"** scheduled action: keep it enabled (restore it if deleted), otherwise nothing is published.
- Unpublishing or archiving a record also clears its schedule.
- Follower notifications now fire once, on the first real publication.
- Manage social links inside each Social Media snippet (or via company social accounts); the per-website social fields in Website settings are gone, and editing one snippet no longer changes the others.
- Structured data and breadcrumbs are generated automatically; nothing to configure.

## What you gain by migrating

- Reliable editorial scheduling: prepare in advance, publish on time, cancel easily.
- Cleaner SEO: accurate sitemaps, no ghost URLs, JSON-LD rich results and breadcrumbs out of the box.
- No spurious follower notifications for unpublished content.
- Simpler, independent social snippets prefilled from company data.
- A sturdier foundation (website on `env`, shared structured-data layer) used by 20.0 modules.
