# base_address_extended migration guide (15.0 -> 16.0)

This module handles detailed street addresses (street name, house number, door number) and city lists. The 16.0 changes come from Odoo PR #81710, *"cleanup of base_address_city & base_address_extension"* (merged January 2022).

## What's new for users

The official 16.0 release notes do not cover this addon: it is an internal technical module with no dedicated entry. Everything below is based on the actual code changes shipped in 16.0.

* **One fixed street format.** The detailed fields (street name, house number, door number) are combined into a single street line with a uniform format, for example `Chaussée de Namur 40 - Appt 12`. The old per-country "street format" setting was error-prone (it was wrong for several countries) and has been removed.
* **Address layout is configurable per country.** The layout (plain `street` versus street name / number / door) now follows the country's address view, instead of being activated by installing a specific localization.
* **Cities are now part of this module.** The former `base_address_city` content was merged in, so there is no separate city module. A country can **Enforce Cities**, meaning every address for that country must pick a city from that country's list; choosing a city automatically fills the city name, zip code and state.
* **Street splitting works everywhere.** Extracting name / number / door from a typed street now works even without this module, so electronic invoicing (EDI) flows can be used with or without `base_address_extended`.

## Technical data model changes

**Added**
* New model `res.city` (`name`, `zipcode`, `country_id`, `state_id`, ordered by name, searchable by zip code, displayed as `City (Zip)`).
* `res.partner`: `city_id` (Many2one `res.city`), `country_enforce_cities` (related to `country_id.enforce_cities`), onchange `_onchange_city_id` that fills/clears `city`, `zip` and `state_id`, and a new helper `_get_street_split()`.
* `res.country`: `enforce_cities` (Boolean, "Enforce Cities").
* `res.partner._compute_street_data` now relies on the shared `tools.street_split()` helper.

**Removed**
* The whole `res.company` inheritance: fields `street_name`, `street_number`, `street_number2`, their inverse methods, and the `_get_company_address_field_names` override. Company addresses no longer expose the split street fields.
* `res.country.street_format` (required Text field).
* Partner helpers `_split_street_with_params()`, `_get_street_fields()`, the `_formatting_address_fields()` override, and the `write()` override that re-split the street when `country_id` changed.

**Changed behaviour**
* `_inverse_street_data()` no longer reads a per-country format: it always builds `street` as "street name + number", then appends " - door number" if present.
* Enforced cities apply only when the country enables `enforce_cities`.

## How your habits should change

* Stop relying on a custom street format: the format is fixed. Keep entering street name, house number and door number separately; the street line is rebuilt for you.
* Company forms no longer have street name / house number / door number fields — edit those on the company's linked contact instead.
* If you used `base_address_city`, keep using `base_address_extended`: city features now live there. Cities only appear when the country's *Enforce Cities* option is enabled.
* Check your localization dependencies: `l10n_nl` and `l10n_cl` no longer depend on this module (install it explicitly if you relied on that). The Colombian city features moved to `l10n_co_edi`, and `l10n_cn_city` now sets *Enforce Cities*.
* After upgrading, review partner addresses: a post-init script handles large databases, but verify a sample before going live.

## What you gain by migrating

* Cleaner, consistent customer and vendor addresses across all countries, with one predictable street format.
* Per-country control: choose the address layout and decide whether cities must be selected from an official list.
* Fewer modules to maintain — cities are merged into `base_address_extended`, avoiding bridge modules.
* Full EDI / electronic invoicing compatibility: street splitting is available whether or not this module is installed.
* Faster, safer upgrades on large databases thanks to the improved post-init script.
