# website migration guide (14.0 -> 15.0)

## What's new for users

**Website Configurator — the headline change.** On installing the Website app, and on each new website, Odoo 15.0 opens a guided configurator instead of the theme kanban:

1. Business description: purpose, industry (autocomplete) and objective.
2. Logo and palette: upload your logo; palette suggestions are derived from its colours.
3. Features: the pages and applications you need.
4. Theme: three themes recommended from your industry, with preview.

Applying it installs the theme, applies the palette, creates the pages, installs the chosen apps, then fills menus, footer links and suggested content. You can skip it at any time. Recommendations and content suggestions come from Odoo's online service (IAP, website.api.odoo.com), so those steps need internet access.

**Website builder (Community).** All themes get a modernised look and are renamed after industries; the *Options* tab becomes the *Theme* tab. New: animated shapes on blocks/images, animations for text/images/columns, extra-wide/tall background images, gradients for background/text/buttons, new templates for Accordion/Masonry/Steps/Products blocks, conditional visibility, a donations block, new page-header templates, click-to-open pop-ups, configurable newsletter pop-ups, conditional form fields and default form values, custom embed HTML blocks, fuzzy search, refreshed Google Analytics integration, and a new default logo.

Enterprise-only items in the official notes (Documents, Appointments, eLearning, Recruitment, Email Marketing) are not part of this Community addon and are not covered here.

## Technical data model changes

New model **`website.configurator.feature`** (Website Configurator Feature): sequence, name, description, icon, iap_page_code, website_config_preselection (renamed from website_types_preselection), page_view_id (ir.ui.view), module_id (ir.module.module), feature_url, menu_sequence, menu_company. A constraint enforces exactly one of page_view_id / module_id. The computed `type` selection field (page/app) was **removed**, in favour of checking page_view_id directly.

- `website` gains `configurator_done` (Boolean, "completed or ignored").
- `res.company.website_theme_onboarding_done` (computed Boolean) was **removed**.
- The `ir.actions.todo` inheritance in website (IrActionsTodo, used by the theme-install onboarding todo) was **removed**.
- `create_and_redirect_to_theme()` is replaced by `create_and_redirect_configurator()`.
- New methods: `_website_api_rpc`, `configurator_init`, `configurator_recommended_themes`, `configurator_skip`, `configurator_apply`, `configurator_set_menu_links`, `configurator_get_footer_links`.
- `new_page()` gained optional `page_values` and `menu_values` arguments (used to set page URL, publication, and menu parenting/sequence).
- The configurator calls the IAP endpoint configured in `website.website_api_endpoint` (default https://website.api.odoo.com).

## How your habits should change

- Installing or adding a website now means answering four configurator steps instead of picking a theme on a kanban. Themes are chosen from three industry-based recommendations, or from the full list.
- The old onboarding checklist ("website theme step done" on the company) is gone. Whether the configurator was completed or skipped is now recorded by `configurator_done` on the website.
- Menus and footers are organised for you, in a predefined order (Home, Shop, Event, Courses, Services, Pricing, Company, Appointment, Contact us). A "Company" submenu appears when there are more than 5 links, and the News / Success Stories features create a website-specific blog. Footer links (Privacy Policy, Forum, and Help if Helpdesk is installed) are injected into the standard footer templates.
- Themes now carry industry names; the Theme tab replaces the Options tab.
- Customisations built on the removed pieces (ir.actions.todo onboarding, `create_and_redirect_to_theme`, `website_theme_onboarding_done`) must be reworked.
- Custom code calling `new_page()` can now pass `page_values` / `menu_values` to set the page URL, publication and menu parenting.

## What you gain by migrating

- A complete, editable website generated in a few clicks from your industry, logo and needs, instead of a manual theme-and-pages setup.
- A coherent structure out of the box: pages, menus and footer links created and ordered consistently from day one.
- A more modern editing experience (shapes, animations, gradients, richer blocks and headers) that keeps your site visually current.
- Less technical debt: the brittle theme-onboarding hacks of 14.0 are gone, replaced by a supported configurator API.
