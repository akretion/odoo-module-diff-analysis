# partner_autocomplete migration guide (18.0 -> 19.0)

This guide covers the `partner_autocomplete` addon (partner/company enrichment from the Contacts and form views) between Odoo 18.0 and Odoo 19.0.

## What's new for users

**A new data provider: Dun & Bradstreet.** Autocomplete has been moved away from Clearbit, which was judged unreliable. Functionality and pricing remain the same, but data quality improves substantially, especially outside Europe. Company logos still come from Clearbit, as D&B does not provide them yet.

**More ways to search and enrich.** Instead of one generic call, the service now exposes separate actions: search by name, search by VAT, enrich by domain, enrich by DUNS number, and enrich by GST number (India).

**A DUNS number field.** Contact and company form views now run the autocomplete widget on the DUNS field as well as Name and VAT, so a company can be identified and updated from its D&B identifier.

**Country-aware suggestions.** Searches by name or VAT now send a country context, defaulting to your own company's country; this reduces wrong matches. A no-country search remains possible.

**Language and industry filled in.** The provider's preferred language is mapped to an installed Odoo language, and industry information is transmitted as a code.

**Background sync removed.** The "autosync" mechanism that pushed your Odoo company data back to the provider database no longer exists.

*Note:* automatic partner category tags from UNSPSC codes are only triggered through CRM enrichment (Enterprise), not by the Community addon alone.

## Technical data model changes

**Model removed:** `res.partner.autocomplete.sync` (fields `partner_id`, `synched`; methods `add_to_queue`, `start_sync`), together with its scheduled sync job. Pending sync records have no equivalent in 19.0.

**Fields removed:**
- `res.partner.partner_gid` (Integer, "Company database ID")
- `res.partner.additional_info` (Char)
- `res.company.partner_gid` (stored related field on `res.partner.partner_gid`, with inverse `_inverse_partner_gid`)

**Methods removed:** `autocomplete`, `enrich_company`, `read_by_vat`, `create`, `write`, `check_gst_in`, `_is_vat_syncable`, `_is_synchable`, `_is_company_in_europe`, `_update_autocomplete_data`, `_iap_replace_logo`, `_enrich_replace_o2m_creation`, `_inverse_partner_gid`, `add_to_queue`, `start_sync`.

**Methods added:** `autocomplete_by_name`, `autocomplete_by_vat`, `enrich_by_domain`, `enrich_by_duns`, `enrich_by_gst`, `_process_enriched_response`, `_iap_replace_language_codes`, `_iap_replace_industry_code`, `iap_partner_autocomplete_get_tag_ids` / `iap_partner_autocomplete_add_tags`.

**Other deltas:** the IAP route changed from `/iap/partner_autocomplete` to `/api/dnb/1`; the `field_partner_autocomplete` widget is now applied to `name`, `vat` and `duns`; the enrichment error message becomes "Unable to enrich company (no credit was consumed)."

## How your habits should change

- Your company data is no longer pushed to the provider database in the background; enrichment is on demand only.
- Creating or editing a partner with a VAT no longer queues a sync: nothing happens until you use the widget.
- The chatter note "Company auto-completed by Odoo Partner Autocomplete Service" is no longer posted.
- Enriching a company no longer creates child contacts (branches) or bank accounts automatically; only the company itself is filled in.
- Searches by name or VAT are now performed in a country context by default, so check the country before trusting an empty result.
- Any saved search, report or export using `partner_gid` must be adapted: the field has been dropped and its values are not migrated. Use the DUNS number instead.
- Custom modules calling `autocomplete()`, `enrich_company()` or `read_by_vat()` must be rewritten to use the new split methods (`enrich_by_domain`, `enrich_by_duns`, `enrich_by_gst`, `autocomplete_by_name`, `autocomplete_by_vat`).

## What you gain by migrating

- Better company data (addresses, VAT, industry, language), notably for markets outside Europe.
- Identification of companies by DUNS and GST numbers, in addition to name, domain and VAT.
- Country-scoped suggestions that reduce incorrect matches.
- A simpler data model: no sync table and no sync job to maintain, fewer obsolete fields on contacts.
- Same functionality and same IAP pricing as before.

For an 18.0 database, plan a short functional test of the contact and company enrichment flows, and audit any customization that reads `partner_gid` or calls the removed methods before migrating.
