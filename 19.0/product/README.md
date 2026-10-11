# product migration guide (18.0 -> 19.0)

## What's new for users

**Packagings are merged into units of measure (Inventory).** Until 18.0, a product could have *packagings* on top of its units of measure, and order/transfer lines carried two parallel quantities (main unit + packaging). In 19.0 both concepts are merged into one simplified model: a packaging is now just another unit of measure with a name and a contained quantity, added to the product. The separate "Product Packagings" setting is gone; the Units of Measure setting is now labelled "Units of Measure & Packagings".

Related improvements:
- **eCommerce**: the extra units/packagings defined on a product are now selectable on the shop page.
- **Purchase**: when you buy in a unit that differs from the product unit, the cost in the product unit is displayed next to the cost in the purchase unit in the product catalog.
- **Incremental mass edit**: in list views you can now use operators when editing en masse (e.g. select products and type `*=1.1` to raise all prices by 10%).

The product import template has also been reworked — see below.

## Technical data model changes

Removed: model `product.packaging`; fields `product.product.packaging_ids`, `product.template.packaging_ids` (with its compute/inverse), and the `group_stock_packaging` setting.
Added: model `product.uom` ("link between products and their UoMs": `uom_id`, `product_id`, `barcode`, `company_id`, unique barcode, barcode also checked against `product.product` barcodes); `product.product.product_uom_ids` (One2many `product.uom`); `uom.uom.product_uom_ids`, `uom.uom.packaging_barcodes_count` and `action_open_packaging_barcodes`. `product.template.uom_ids` (Packagings) is kept.
Rounding: the per-unit `rounding` field on `uom.uom` is no longer configurable — it is computed from the decimal accuracy, renamed "Product Unit of Measure" → "Product Unit". Field digits changed from `'Product Unit of Measure'` to `'Product Unit'` (pricelist item Min. Quantity, vendor pricelist Quantity, price computations). The `product` overrides of `decimal.precision` and `uom.uom` (onchange warnings) were removed.
Import: new non-stored Char field `import_attribute_values` ("Product Values") on `product.template` and `product.product`. `ProductTemplate.load()` now splits rows with attribute values (variants) from rows without (templates); `ProductProduct.load()`, `_load_records_create()` and `_load_records_write()` group variants onto templates by name, create missing attributes/values, and raise a validation error when an existing product's attribute values differ from the file. Context keys `create_product_product` and `update_product_template_attribute_values` now gate variant creation/update on attribute lines. The downloadable import template is now `product_product.xls`.

## How your habits should change

- Stop creating **product packagings**: create (or reuse) a unit of measure with the wanted name and quantity and add it to the product's list of units. Existing packaging records have no equivalent record type in 19.0, so review your packagings with your integrator as part of the migration.
- No more rounding per unit: set the Decimal Accuracy "Product Unit" once; it applies to every unit of measure.
- Use the new import file for templates **and** variants: one line per variant with an "Attribute values" column written as `Color:Red,Size:S`. Lines without attribute values create/update the product templates. Attributes and values that do not exist yet are created automatically. External IDs must be `product.product` IDs when the line has attribute values, `product.template` IDs otherwise. A row whose attribute values differ from the existing product is rejected with an error, so keep the source file consistent.

## What you gain by migrating

- One single, simpler concept for units and packagings, and no duplicate quantity fields on orders and transfers.
- One rounding rule everywhere, so quantities and prices compute consistently across all units.
- A single import that creates templates, variants, attributes and values at once — much faster catalog onboarding and bulk updates.
- Packagings selectable on the shop and priced transparently in the purchase catalog.
- A supported version and a data model that future Odoo releases keep building on.
