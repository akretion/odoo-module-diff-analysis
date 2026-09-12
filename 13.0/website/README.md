# website migration guide (12.0 -> 13.0)

## What's new for users

- **Modern frontend**: the website was upgraded to Bootstrap 4.3.1, with better mobile rendering (for instance for events) and deferred website JavaScript loading for faster pages.
- **Theming and identity**: you can now change the logo size easily, and your website can display its own logo, different from the company logo. A new font selection tool lets you pick a Google Font for the site. Empty custom CSS and JS files are provided so themes can be extended cleanly.
- **Editing**: new image upload modal with control over size and quality; anchor editing in the page builder.
- **SEO**: if you leave the meta title and description empty, the page now falls back to sensible default title and description instead of an empty result.
- **Contact forms** are created automatically pre-configured with default fields; a newsletter can be dropped on the site like any other snippet.
- **Languages in URL**: the language can now be specified in the website URL, instead of relying only on the language code.
- **Error pages** use a new handler based on the Bootstrap toast component.

Note: push notifications, social marketing campaigns, live chat requests and "send message to visitor" features belong to the Social/Enterprise apps and are **not** part of the Community edition. The underlying visitor tracking described below is, however, included in the `website` addon.

## Technical data model changes

- **Code reorganisation, no functional impact**: the mixins were extracted into `models/mixins.py` (`website.seo.metadata`, `website.multi.mixin`, `website.published.mixin`, `website.published.multi.mixin`); `website.menu`, `website.page` and `website.redirect` now live in their own files. Model names are unchanged.
- **New `website.visitor` model**: tracks visitors via a cookie (`visitor_id` now stores a UUID `access_token`, valid one year, replacing the old signed `id-hash` value), plus language, country, first/last connection date, visit count, linked `res.partner` (new `visitor_ids` field on partners) and browsing history. `res.users.authenticate` was overridden to link a logged-in partner to his visitor.
- **`website.visitor.page` replaced by `website.track`**: the history model now stores a free `url` in addition to `page_id`, so non-page URLs can be tracked. A track is only created if the same URL/page was not already viewed in the last 30 minutes.
- **Tracking flag moved**: `is_tracked` was removed from `website.page`; tracking is now defined on `ir.ui.view` with a new `track` boolean. Tracking is therefore set at view/template level, which covers more than website pages.
- **Visitor statistics reworked**: `last_connection_datetime` became a stored field (default: now, visitors ordered by it); the temporary `website.visitor.lastconnection` table was introduced then removed again. A new visit is counted after 8 idle hours. `page_ids` is no longer a stored computed field.
- **Method signature changes**: `Http._extract_website_page` was replaced by `_register_website_track(response)`; `_handle_webpage_dispatch(response, website_page)` is kept; `_get_visitor_from_request(with_previous_visitors=False)`, `_create_visitor(website_track_values=None)` and a new `_add_tracking()`; `_cron_archive_visitors` still archives visitors inactive for 7 days.

## How your habits should change

- To include a page in visitor history, enable **Track** on the corresponding view/template rather than on the page record.
- The Visitors list is now ordered by last connection; a "connected" visitor is one seen in the last 5 minutes, and a new visit starts after 8 hours of inactivity.
- Leaving SEO title/description empty is now a valid choice: defaults are used, so review pages where empty values were intentional.
- Website and company logos are now independent; check your theme after migration.
- Visitor history is per website: the same person visiting two websites creates two visitors.

## What you gain by migrating

- A faster, more modern frontend (Bootstrap 4.3.1, deferred JS) and easier branding: separate website logo, font picker, image quality control.
- Built-in visitor tracking with visit history, page statistics and partner linkage — the foundation for follow-up and chat, available in Community.
- Cleaner SEO behaviour with automatic fallback titles and descriptions, and clearer multi-language URLs.
- A maintained code base: the split into files and dedicated mixins makes future upgrades and custom developments (your own models inheriting `website.published.multi.mixin`) simpler and safer.
