# partner_autocomplete migration guide (18.0 -> 19.0)

Scope: the Community addon `partner_autocomplete` (company/contact autocomplete and enrichment through Odoo IAP).

## What's new for users

**A new data provider: Dun & Bradstreet.** Company data no longer comes from Clearbit, it now comes from Dun & Bradstreet through Odoo IAP. The official release notes state that the functionality and the pricing remain the same, while data quality is drastically improved, especially for markets outside Europe. Clearbit is still used for logos, which D&B does not provide yet.

**Country-aware lookups.** Name and VAT searches now take a country into account; they default to your company's country, and passing 0 explicitly removes the country filter for broader results.

**More fields covered.** The autocomplete widget, previously attached to the Name and VAT fields on contact/company forms, is now also attached to the DUNS number field.

**Richer enrichment result.** When the provider returns a preferred language, it is mapped to an installed language and set on the partner; activity (UNSPSC) codes returned by the provider become partner tags.

Note: the release notes mainly mention the provider change. The rest of the behaviour described here comes from the code changes themselves.

## Technical data model changes

Removed model:
- `res.partner.autocomplete.sync` (`partner_id`, `synched`) with its `start_sync` / `add_to_queue` methods: the whole background autosync queue is gone.

Removed fields:
- `res.partner.partner_gid` (Integer, "Company database ID")
- `res.partner.additional_info` (Char)
- `res.company.partner_gid` (related to `partner_id.partner_gid`, stored, inverted by `_inverse_partner_gid`)

Removed methods:
- `res.company._inverse_partner_gid`, `res.company._enrich_replace_o2m_creation`
- `res.partner._iap_replace_logo`, `_is_company_in_europe`, `_is_vat_syncable`, `check_gst_in`, `_filter_synchable`, `_update_autocomplete_data`, and the `create` / `write` overrides that fed the sync queue.

Changed method signatures:
- `autocomplete(query, timeout)` → `autocomplete_by_name(query, query_country_id, timeout)`; `read_by_vat(vat, timeout)` → `autocomplete_by_vat(vat, query_country_id, timeout)`.
- `enrich_company(company_domain, partner_gid, vat, timeout)` → `enrich_by_domain(domain, timeout)`, plus new `enrich_by_duns(duns, timeout)` and `enrich_by_gst(gst, timeout)`; all rely on a common `_process_enriched_response`.
- `_format_data_company` now also converts language codes (`_iap_replace_language_codes`); new `iap_partner_autocomplete_add_tags(unspsc_codes)`.
- IAP route changed from `/iap/partner_autocomplete` to `/api/dnb/1`.

Views: the company/contact form no longer injects a hidden `partner_gid` field, and the autocomplete widget now also targets `duns`.

## How your habits should change

- No more background sync. Creating or updating a company with a provider ID and a syncable VAT used to silently queue it to push data back to the provider, tracked by a "synched" flag. That queue and its model are removed: there is nothing to sync or monitor anymore.
- No more chatter trace. Enrichment no longer posts "Company auto-completed by Odoo Partner Autocomplete Service" notes on the record.
- Less is written automatically on companies. Enrichment no longer creates child contacts, no longer creates related records (such as bank accounts) from one2many payloads, and the backend no longer downloads and injects a logo image. Review the enriched result before saving.
- Reports, filters, exports or automations using `partner_gid` or `additional_info` must be updated or dropped; `duns` is the identifier to use now.
- Custom code calling `autocomplete`, `enrich_company` or `read_by_vat` must be adapted to the new methods.
- Remove any scheduled action referring to `res.partner.autocomplete.sync`; it disappears with the model.

## What you gain by migrating

- Noticeably better company data, especially outside Europe, thanks to Dun & Bradstreet, with unchanged IAP functionality and pricing.
- Enrichment by domain, DUNS number or GST/VAT number, and country-aware name and VAT lookups.
- A simpler data model: no sync queue, no `partner_gid` / `additional_info` technical fields, no hidden chatter noise.
- Enrichment results that respect the partner's language and can tag partners with their activity codes automatically.
