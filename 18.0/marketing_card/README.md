# marketing_card migration guide (17.0 -> 18.0)

## What's new for users

The official Odoo 18 release notes cover this addon in a single line: *"New social app — Promote events through community marketing, and track speakers, attendees, and sponsors."* That is the only release-note entry that actually relates to `marketing_card`; the other Marketing Automation, Social Marketing and Email Marketing notes concern different apps and are not part of this module.

Because the release notes are very terse here, the picture below comes mainly from the code diff — which shows the addon's files being introduced during the 18.0 cycle (PR 175486) and then substantially reworked before release (PR 178007). For a 17.0 user this is therefore not a like-for-like update, but a new app to discover.

What it does in its 18.0 shape: a **card campaign** is a design template plus a set of content slots, bound to a target model (Contacts, Event Tracks, Event Booths, Event Registrations) and a mandatory preview record. You select which records to invite, send a mailing that hands each recipient a personal card link, and each shared link serves a per-person image optimised for social networks, then redirects to the URL you choose.

## Technical data model changes

From the pseudo patches: 35 method signatures added, 0 modified, 0 removed.

**Removed**
- Model `card.campaign.element` and the `card_element_ids` One2many on `card.campaign`: card content is no longer a sub-list of "elements".
- Fields `card.campaign.preview_record_url` and `card.card.record_ref`; the `base` whitelist `_marketing_card_allowed_field_paths`; the HMAC hash token (URLs are now slug-based: `/cards/<slug>/card.jpg|preview|redirect`).

**Added**
- `card.campaign`: `mailing_ids` / `mailing_count`, `card_ids`, `request_title`, `request_description`, and the content fields `content_background`, `content_button`, `content_header` (with `_dyn`, `_path`, `_color`), the same trio for sub-header, section, sub-section 1 and 2, plus `content_image1_path` / `content_image2_path`.
- `mailing.mailing`: `card_campaign_id`, computed `mailing_model_id`, `card_requires_sync_count`, `action_update_cards`, and blocking checks in `action_put_in_queue` / `action_send_mail`.
- `card.card`: `active` (previews are inactive cards) and `requires_sync`.
- `utm.source`: the Marketing Card UTM source can no longer be deleted.

**Changed behaviour**
- `res_model` is now computed, stored and read-only; `preview_record_ref` is required. Changing the model of a campaign that already has cards raises a validation error.
- Card images are rendered when cards are created/updated by the user (no `sudo()`), instead of lazily by the anonymous visitor. The old "clear images after 1 day" job becomes a card deletion after 60 days (`_gc_card_url_images` -> `_gc_card`).
- Rendering runs in batches of 100 cards through `_update_cards(domain, auto_commit)`.

## How your habits should change

- Configure content directly on the campaign form rather than in a separate elements list: each slot is a plain field, with an "Is Dynamic" checkbox and a field path when values must come from the record.
- Preview is now a button opening a temporary (inactive) card, not a URL to copy.
- Cards exist before the mailing is sent: use "Update cards" on the mailing — scheduling or sending is blocked while cards are out of date.
- Share links are shorter and slug-based; any visitor holding the link can display a card, so no token is used.
- The field-path whitelist is gone: paths are evaluated with the current user's rights instead of as superuser. Review any path pointing at sensitive data.

## What you gain by migrating

- A ready-made word-of-mouth / community-marketing app for events: speakers, attendees, sponsors and partners each get a personalised, on-brand card to share.
- Per-campaign tracking of cards sent, shared and visited, with a reward message and link to thank people who share.
- A simpler data model (no element sub-model, no hash tokens, no `sudo()`, no field whitelist) that is faster to configure and respects access rights.
- Consistent per-recipient images for social sharing, generated on demand and retained for 60 days.
