# website migration guide (12.0 -> 13.0)

Scope: the Community `website` addon. Odoo 13.0 brings a large editor/design refresh, performance work, and a reworked visitor-tracking engine.

## What's new for users

**Website editor and design**
- Upgrade to Bootstrap 4.3.1; events behave better on mobile.
- New font selection tool, with Google Font support directly on the website.
- New image upload modal with control over image size and quality; logo size is now easy to change.
- Redesigned blog layouts, refreshed snippets and themes, and a ready-to-use newsletter snippet.
- Anchor editing from the page editor.
- A website can now use its own logo, different from the company logo.
- Empty custom CSS and JS files are generated so you can add your own code.

**Content, SEO and performance**
- The site language can now appear in the URL in a readable form instead of the raw ISO code.
- If you leave the page title and description empty, default values are used (better SEO out of the box).
- Website JavaScript loading is deferred, which speeds up page loads.
- New contact forms are automatically configured with default fields.
- New error handler based on Bootstrap's toast component.
- Forum: you can order it, and the discussion mode is back.

**Visitor tracking**
- The online-visitors list is based on the rebuilt tracking engine (see below), so browsing history is more complete.

Note: Social Marketing items from the release notes (push notifications, campaigns, social posts, live chat requests sent to visitors, "Online Visitors" outreach) belong to the Social offering, not to this Community addon. They are not included here.

## Technical data model changes

**Models**
- Added `website.track` ("Visited Pages") with `visitor_id`, `page_id`, `url` (new Text field, indexed)` and `visit_datetime`.
- Removed `website.visitor.page`, replaced by `website.track`.

**Fields added**
- `ir.ui.view.track` (Boolean, default False): tracking is now defined on the view/template.
- `website.visitor.access_token` (Char, uuid, indexed, copy=False, restricted to `base.group_website_publisher`). The visitor cookie now stores this token instead of a signed visitor id, and expires after 365 days.
- `website.visitor.website_track_ids` (One2many to `website.track`), replacing `visitor_page_ids`.
- `website.track.url` stores the full URL, so non-page URLs (product pages, etc.) can be tracked.
- `res.partner.visitor_ids` (Many2many through `website_visitor_partner_rel`).

**Fields changed or removed**
- `website.page.is_tracked` removed (use the view's Track flag).
- `website.visitor.page_ids` is no longer stored; it is computed.
- `website.visitor.last_connection_datetime` remains a stored, read-only Datetime, and visitors are still ordered by it. (An intermediate design using a temporary `website.visitor.lastconnection` table and a computed field was reverted before release.)
- Visit counting: a new visit is counted when the previous connection is older than 8 hours; visitors idle for 7 days are archived by cron.
- A visitor's display name is now shown as "Name #id".

**Behaviour and method changes**
- `Http._extract_website_page()` is replaced by `Http._register_website_track()`: tracking is now evaluated from the response template/view, not only from a `website.page`.
- `_get_visitor_from_request(with_previous_visitors=False)` gained a parameter: for a logged-in user, all previous visitors of the partner can be retrieved.
- `res.users.authenticate()` now links the logged-in partner to the current `website.visitor` (sets `user_partner_id` and `name`).
- Repeated views of the same URL within 30 minutes are not tracked again.

## How your habits should change

- Tracking moved from the page to the view: don't look for "Is Tracked" on the page, tick **Track** on the view/template instead.
- Visitor history now lists URLs, including product and other non-page URLs, instead of only website pages.
- The Visitors screen is where you review a visitor's history and the linked contact.
- Default SEO titles/descriptions now apply when fields are empty: clear any placeholder text you do not want reused.
- Review custom themes/snippets after upgrading: layouts were reworked and the Bootstrap version changed, so re-test custom CSS/JS and templates.
- Plan for the new empty custom CSS/JS files and re-check your theme settings.

## What you gain by migrating

- A more reliable, more detailed visitor history: per-URL tracking, product pages included, and a proper link between visitors and contacts.
- A faster, more modern editor: Bootstrap 4.3.1, fonts, image modal, better blogs, logo per website, deferred JS.
- Better SEO defaults and readable language URLs out of the box.
