# lunch migration guide (14.0 -> 15.0)

The Odoo 15.0 release notes do not describe any user-visible change for the `lunch` addon (the published notes for this cycle focus on other apps). Everything below therefore comes from the source-code changes shipped in this addon between 14.0 and 15.0. Two of them are important for you: the weekday fields were renamed, and the "extras" (toppings) configuration moved from product categories to lunch suppliers.

## What's new for users

No feature announcement from the release notes applies to `lunch`. The changes are internal reorganisations that affect how you configure suppliers, categories and extras. In practice, the way you *use* the app day to day (ordering, confirming, receiving reminders, viewing today's supplier) is unchanged; what changes is *where* you configure the extras.

## Technical data model changes

**1. Weekday fields renamed (recurrence mixin alignment)**

On both `lunch.alert` and `lunch.supplier`, the seven boolean day fields were renamed to match the naming used by the Calendar and Project modules:

- `recurrency_monday` -> `mon`
- `recurrency_tuesday` -> `tue`
- `recurrency_wednesday` -> `wed`
- `recurrency_thursday` -> `thu`
- `recurrency_friday` -> `fri`
- `recurrency_saturday` -> `sat`
- `recurrency_sunday` -> `sun`

Default values and behaviour are unchanged: on alerts all seven days default to enabled; on suppliers Monday-to-Friday default to enabled and Saturday/Sunday to disabled. The internal weekday lookup tables now use `mon/tue/wed/thu/fri/sat/sun`, and the compute/search dependencies (`_compute_available_today`, `_search_available_today`) were updated accordingly. The purpose is to share the `web_weekly_recurrence` widget instead of declaring one boolean field per day in the view.

**2. Extras (toppings) moved from product category to supplier**

Fields removed from `lunch.product.category`:
- `topping_label_1/2/3`, `topping_ids_1/2/3`, `topping_quantity_1/2/3`
- the custom `create` / `write` overrides that tagged toppings by category

Fields added to `lunch.supplier`, with identical labels, defaults (`Extras`, `Beverages`, `Extra Label 3`, quantity `None or More`) and domains:
- `topping_label_1/2/3`, `topping_ids_1/2/3`, `topping_quantity_1/2/3`
- the topping-category tagging logic, now executed in `lunch.supplier.create` (multi-create) and `lunch.supplier.write`

**3. `lunch.order` follow-up changes**

`topping_label_*` and `topping_quantity_*` on the order are now related to `product_id.supplier_id.*` instead of `product_id.category_id.*`. `_compute_available_toppings` now counts `lunch.topping` records by `supplier_id` (was `category_id`).

**4. `lunch.topping` field changes**

- `price`: `Float(digits='Account')` -> `Monetary` (uses the existing related `currency_id`)
- `category_id` (Many2one to `lunch.product.category`, cascade) -> `supplier_id` (Many2one to `lunch.supplier`, cascade)

## How your habits should change

- **Configure extras on the supplier**, not on the product category. The Extra labels, the Extra quantity rules and the topping lists now live on the supplier form.
- **Reassign your existing toppings.** Because `lunch.topping.category_id` was replaced by `supplier_id`, toppings that were attached to a category must be linked to the corresponding supplier(s) after migration.
- **Review your category setup.** If two suppliers previously shared one product category that carried extras, each supplier now needs its own extras configuration — the settings are no longer inherited from the category.
- **Nothing to relearn about weekdays.** The recurrence checkboxes look and behave the same; only the underlying field names changed.

## What you gain by migrating

- **Configuration that matches reality.** Extras belong to the supplier who actually offers them, so a supplier's extra labels, quantities and topping list are defined in one place — closer to how you buy from them.
- **Consistency across Odoo apps.** The weekday field naming aligns `lunch` with Calendar and Project, enabling the shared weekly-recurrence widget and a more uniform user experience.
- **Correct money handling.** Topping prices are now `Monetary` fields, so amounts follow the company currency instead of a generic account-digit float.
- **Cleaner data model.** Removing the category-based topping logic and its custom `create`/`write` overrides reduces duplication and the risk of inconsistent extras when reorganising categories.
