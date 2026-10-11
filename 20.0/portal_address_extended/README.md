# portal_address_extended migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes contain **no section dedicated to this addon**. The work behind `portal_address_extended` in 20.0 is a *refactor*, not a new feature: the module itself disappears and its behaviour is absorbed into the standard `portal` module. So for day-to-day users the portal address form looks and behaves the same, with a few layout/setup improvements:

- A country's postal code requirement is no longer a simple on/off checkbox. It is now a three-state setting: *not applicable*, *optional* or *required*.
- On portal (front-end) address forms, the postal code field is only displayed when the country's postal code is not set to *not applicable* — less clutter and fewer meaningless entries.
- The Country form view has been reworked so the country title is bigger and easier to read.
- The country/state-filtered **Cities** dropdown in front-end address forms is preserved (city list filtered by country and state).
- Country data was updated to match the new postal code setting.

Related release-note items that touch the same ground, but are **not** this addon:
- Chile: Chilean commune data has been added, reducing manual entry errors when configuring addresses in the Contacts and eCommerce flows (available from 19.0).
- The many address-related entries in the Accounting/Payroll localizations (ZATCA address rules, ETA "Building Number", e-invoice addenda, etc.) are Enterprise or localization features and are **not** delivered by this Community addon.

## Technical data model changes

Source: PR https://github.com/odoo/odoo/pull/257484 — "[REF] base,portal{,address_extended},*: refactor portal address".

The addon is **merged into `portal`** in 20.0. As a consequence, in Odoo 20.0:

- `models/__init__.py` is deleted — the addon no longer declares any model of its own.
- `models/res_country.py` is deleted — the `_inherit = 'res.country'` inheritance is removed. The methods `_enforce_city_choice()`, `_get_cities(state_id=None)` and `_get_cities_fields_to_fetch()` are no longer provided by this module. They were the ones deciding whether city choice is enforced (front-end, only for country codes **BR, CL, PE, CO, TW** and only when the country has cities configured) and returning the list of cities to display.
- `models/res_partner.py` is deleted — the `_inherit = 'res.partner'` inheritance is removed. The methods `_get_frontend_writable_fields()` (which added `city_id` to the fields writable from the portal) and `_get_mandatory_address_fields(country_sudo, **kwargs)` (which made `city_id` mandatory and `city` non-mandatory when the country enforces city choice) are no longer provided by this module.
- **Field change on `res.country`**: the boolean `zip_required` is replaced by a selection field `zip_applicability` with values `not_applicable`, `optional` and `required`; country data was updated accordingly.
- Dependency change: `portal` now depends on `base_address_extended`, and the equivalent address logic lives there. `base_address_extended` is now independent of the Contacts app; the *Cities* menu moved to the Contacts app and depends on `base_address_extended`.
- Portal layout and localisations were adapted to the new address logic.

**Migration impact:** any custom module that inherits `portal_address_extended`, calls the three `res.country` methods or the two `res.partner` methods, or reads/writes `zip_required`, must be updated before upgrading. The merge itself is handled by the standard upgrade scripts (odoo/upgrade#10598).

## How your habits should change

- **Do not install `portal_address_extended` on 20.0.** If you used it for city selection in the portal, the feature now ships with `portal` (with `base_address_extended`).
- Configure postal code behaviour per country with the new three-value setting instead of ticking a box: pick *not applicable* for countries without postal codes and the portal form will hide the field.
- Manage cities from the Contacts app (*Cities* menu), which is now the single entry point, rather than from this addon.
- Developers: port any reference to `zip_required`, `city_id` front-end writability or the removed helper methods to the new location in `portal` / `base_address_extended`.

## What you gain by migrating

- **Less to maintain**: one fewer addon, and the portal address logic is now core `portal` code, so it follows Odoo's own portal improvements and upgrade path instead of being a side module.
- **Cleaner, smarter portal address forms**: postal code shown only when the country actually uses one, city/commune suggestions where they exist, fewer invalid addresses for Brazil, Chile, Peru, Colombia and Taiwan.
