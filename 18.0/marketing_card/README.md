# marketing_card migration guide (17.0 -> 18.0)

## What's new for users

The Odoo 18.0 release notes present Marketing Card as a **new social app**: promote events through community marketing and track speakers, attendees and sponsors. The 17.0 → 18.0 code comparison likewise shows this addon as newly added, so there is no legacy card data to convert. What did change is the campaign design itself, which was reworked during the 18.0 cycle; this guide describes the resulting behaviour. Check availability for your Odoo edition before planning the rollout.

- Simplified campaign form: the "elements" table is gone. Header, Sub-Header, Section, Sub-Section 1/2, Button and Background are now plain fields on the campaign, each with an optional *dynamic* switch and a field path to pull the value from the record.
- Preview is a button: the "Preview On" record is mandatory, and Preview opens a real (archived) card in a new tab instead of showing a stored URL.
- Cleaner share links: cards are considered public, so the URL no longer needs a signed hash token.
- Images are generated when you send the cards, under your own rights, instead of being rendered on the fly for public visitors.
- Mailings are now linked to the campaign: a Mailings smart button lists them, and "Send Cards" pre-fills the subject, target model and a ready-to-edit message ("Help us share the news").

## Technical data model changes

Removed: model `card.campaign.element` and the campaign's `card_element_ids`; fields `preview_record_url`, `record_ref`; methods `_selection_preview_record_ref`, `_get_generic_image_b64`, `_generate_card_hash_token`, `_get_card_path`.

Added on `card.campaign`: `card_ids`, `mailing_ids` + `mailing_count`, `request_title` / `request_description`, and the content fields `content_background`, `content_button`, `content_header(_dyn/_path/_color)`, `content_sub_header`, `content_section`, `content_sub_section1/2` (each with `_dyn`/`_path`), `content_image1_path`, `content_image2_path`.

Changed: `res_model` is computed and read-only (derived from Preview On), `preview_record_ref` is required, `target_url` is now labelled "Post Link". On `card.card`: new `active` and `requires_sync`; `share_status` labels shortened to "Shared" / "Visited"; `record_ref` replaced by a computed `display_name`. Mailings can be attached through the new `card_campaign_id` on `mailing.mailing`.

Method signatures: `_get_card_element_values(record, preview_values)` → `(record)`; `_get_or_create_cards_from_res_ids(res_ids)` → `_update_cards(domain, auto_commit=False)`; `_get_preview_url_from_res_id()` → `_get_url_from_res_id(res_id, suffix='preview')` and `card._get_path(suffix)`; new `_get_render_fields()`, `action_preview()`, `action_view_mailings()`.

## How your habits should change

- Build the card content on the campaign form; there is no element list to configure.
- Set "Preview On" first, since it drives the target model.
- The target model can no longer be changed once the campaign has cards (validation error).
- Dynamic values are read with your own access rights: the administrator whitelist of allowed field paths has been dropped, so any reachable field can be used.
- Generate and send from the campaign, then follow up from the Mailings smart button and the card statistics.

## What you gain by migrating

- One form to design a campaign instead of a list of element lines.
- Images are produced at send time, in batches
