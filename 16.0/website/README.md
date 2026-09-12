# website migration guide (15.0 -> 16.0)

## What's new for users

- **Controller as homepage**: define the homepage in Settings as a URL (e.g. `/shop`, `/contactus`), not only by flagging a website page. The URL stays `/`.
- **Cleaner settings** and a merged backend/frontend website menu, with the new Site menu to see all pages and objects at a glance.
- **Social links** leave the settings screen: use the Social Media building block on your page.
- **Builder**: grid layout mode, per-device visibility (mobile/desktop/both), scroll animations, GIF and "Devices" image shapes, mobile view editing, transparent mega menus.
- **Contact form**: phone country code preset from the visitor's GeoIP.
- The embedded **Google Analytics dashboard is removed** (Google deprecated its APIs); tracking itself still works.

## Technical data model changes

**website**: `homepage_id` (Many2one on website.page) replaced by `homepage_url` (relative Char URL), exposed as the new `website_homepage_url` setting. `country_group_ids` removed (multi-website by country dropped); `domain` now has a unique SQL constraint. `google_management_client_id` and `google_management_client_secret` removed.

**website.page**: `cache_time` and `cache_key_expr` removed; page-level HTTP caching is replaced by the `t-cache` QWeb directive.

**website.visitor**: `name` now related to `partner_id.name`; `partner_id` is stored and computed from `access_token`; `active` and `parent_id` removed; the `unique(partner_id)` constraint removed; order is now `id DESC`. `access_token` is no longer a cookie: it is a hash of IP, user agent and session (the partner id once logged in).

**res.config.settings**: `specific_user_account` replaced by `shared_user_account`; `has_social_network`, `has_google_maps`, `has_google_analytics_dashboard` removed; compute/inverse methods renamed (`_compute_auth_signup_uninvited`, matching `_inverse_*`); `open_template_user`, `website_go_to`, `install_theme_on_current_website` removed; `module_website_livechat` and `module_marketing_automation` added.

**Method signatures**: `_handle_webpage_dispatch(response, page)` → `_handle_webpage_dispatch(page)`; `_get_current_website_id(domain_name, country_id, fallback)` → `_get_current_website_id(domain_name, fallback)`; `_link_to_partner`/`_link_to_visitor` replaced by `_merge_visitor`; visitors are created through a raw SQL upsert and tracks are no longer deduplicated over 30 minutes.

**Theme models**: `theme.website.menu` gains `mega_menu_content`, `mega_menu_classes`, `use_main_menu_as_parent`; `theme.website.page` gains `is_published`, `header_overlay`, `header_color`, `header_visible`, `footer_visible`.

**Translations**: `ir.translation` is no longer used; translated fields are stored as JSONB columns, with website view/menu translations handled by `_load_module_terms`.

## How your habits should change

- Set the homepage in Settings as a relative URL; page properties now store that URL.
- Multi-website by country group is gone: one website per domain, and domains must be unique.
- Consult Google Analytics dashboards outside Odoo.
- Configure social links on the page, not in settings.
- Anonymous visitor figures will shift: identification is IP + browser based, and merged visitors are deleted instead of archived.

## What you gain by migrating

- A simpler, better organised Website app with genuine homepage flexibility (shop or any dynamic page served at `/`).
- Faster page tracking and less database bloat: fewer queries per page and no duplicate visitor records.
- Cleaner theme/menu tooling and a richer builder (grid layout, device visibility, animations).
- You stay on a supported Odoo branch and can adopt the Odoo 16 eCommerce, Events and Appointments features built on the same website foundation.
