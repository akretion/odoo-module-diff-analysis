# product migration guide (16.0 -> 17.0)

This guide covers the core `product` addon (products, product templates, attributes, attribute values, variant generation and exclusions). Its data model is shared by Sales, Purchase, Inventory, eCommerce and Manufacturing, so it is one of the pieces that must be migrated first.

## What's new for users

Honestly: nothing functional. The 17.0 release notes contain no section dedicated to the `product` addon. The product-related novelties of 17.0 live in the apps that build on top of it (for example adding products to a quotation from a catalog view in Sales, or updating a product's quantity from its form in Inventory); they are not part of this addon.

Concretely, everything you use every day is unchanged in 17.0:

- Attributes and their values, display type (radio / pills / select / color) and variants creation mode (instantly / dynamically / never).
- Product template attribute lines, value price extras and exclusion rules.
- The guard rules: you still cannot change the variants creation mode of an attribute, or delete/rename an attribute value that is used on products.

## Technical data model changes

Source: pseudo patches. Between 16.0 and 17.0 the only change detected in `product` is the commit "[CLN] product: style pass on split files" (PR 113612, ~343 changed lines). It is a pure code-style refactoring:

- No model added or removed, no field added or removed, no method signature change (method_signatures: NO), no SQL constraint change.
- Fields (`product_tmpl_ids`, `is_used_on_products`, `attribute_line_id`, `product_tmpl_id`, `attribute_id`, `display_type`, `value_count`, `is_custom`, ...) keep identical semantics; they are only re-declared with explicit `comodel_name=` / `inverse_name=` / `string=` keywords. Required, ondelete, index and store options are unchanged.
- `product.attribute.value.display_type` and `product.template.attribute.value.display_type`: the explicit `readonly=True` was dropped; related fields are readonly by default, so behaviour is identical.
- `product.template.attribute.line.value_count`: `readonly=True` dropped, but it remains a stored compute field, hence still non-editable.
- User-facing error messages were rewritten with named placeholders (`%(attribute)s`, `%(products)s`) instead of `%s`, with slightly re-worded spacing. When errors are raised is unchanged.
- Technical clean-up: `[(5, 0, 0)]` becomes `Command.clear()`, `[(3, id, 0)]` becomes `Command.unlink(id)`, `super(Model, self)` becomes `super()`.

Because of this reformatting, the diff tool reports "breaking" deltas (score del 5.2 / add 1) on lines such as `product_tmpl_ids` or `attribute_line_id`; these are false positives caused by line splitting, not logical changes.

## How your habits should change

Almost not at all: your day-to-day product configuration workflow is identical to 16.0. Two points to check:

1. Error message wording changed. Any custom translation (.po) or script matching the exact text of product error messages must be reviewed; old message ids no longer exist.
2. Labels. A few technical fields lost their explicit label (e.g. `is_custom`, `product_tmpl_id`, `attribute_id` on product template attribute values); the label now comes from the related field. The wording usually stays the same, but verify custom views/xpaths that rely on it.

For developers: no data migration script is needed for `product`, and custom modules extending these models keep working.

## What you gain by migrating

- A low-risk migration: the product data model is stable from 16.0 to 17.0, so no rework of attributes, variants, price extras or exclusions.
- A cleaner code base aligned with 17.0 conventions (`Command`, `super()`, explicit field keywords), which makes future custom developments and upgrades easier to maintain.
- Access to the 17.0 improvements built on top of products in the other Community apps you use (Sales catalog, Purchase vendor catalog, Inventory/Barcode product displays), which is where the real functional value of 17.0 lies for product management.
