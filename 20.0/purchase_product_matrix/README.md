# purchase_product_matrix migration guide (19.0 -> 20.0)

## What's new for users

This addon is small and focused: it lets you order configurable products from a purchase order by filling in a variant matrix, and it prints that matrix on the purchase order report.

The Odoo 20.0 release notes do not describe the purchase product matrix feature itself. What they do describe, in the Purchase section, is a rework of the purchase order form; one part of that rework is what removes the only user-visible option this addon used to add:

- The **"Other Information" tab has been renamed "Other Info"** and split into three sections (Purchase, Billing, Tracking) so fields are grouped by purpose.
- **Payment Terms** moved out of that tab onto the main purchase order form, for quicker access.
- The **"Print Variant Grids"** checkbox was removed from the purchase order form. It was only visible in debug mode. Per Odoo, users who do not want the grid can now manage this from the report itself, which keeps the form cleaner.

Everything else listed in the Purchase release notes (alternatives comparison, default Incoterm per vendor, purchase agreement sections, total amounts on sections, vendor quality rate, unit cost display, and so on) is unrelated to this addon. Migration notes below therefore rely mainly on the code changes.

## Technical data model changes

Models added or removed: none. Fields added: none.

**Field removed**

- `report_grids` (Boolean, string "Print Variant Grids", default `True`, help "If set, the matrix of configurable products will be shown on the report of this order.") is **removed** from `purchase.order`. This field was defined by this addon.

**Method behaviour changed**

- `PurchaseOrder.get_report_matrixes()` (reporting method, no arguments) has an **unchanged signature** but a changed body: the `if self.report_grids:` guard has been dropped.
  - In 19.0: an order only produced matrix report data when `report_grids` was True.
  - In 20.0: matrix data is always built for configurable product templates that have more than one order line, and rows with all-zero quantities are still filtered out. The method no longer reads `report_grids` at all.

**Upgrade impact**

- The `report_grids` column on `purchase_order` is dropped on upgrade; its stored per-order values are lost. There is nothing to migrate, since the behaviour it controlled no longer exists.
- Any custom view, filter, or report that referenced `report_grids` (for example an inherited field in the "Other Info" tab) must be updated, or it will break on upgrade.
- Any custom code calling `get_report_matrixes()` keeps working: the signature is identical.

## How your habits should change

- **Stop looking for the "Print Variant Grids" checkbox.** It is gone from the purchase order form, in debug mode as well. There is no per-order on/off switch anymore.
- **Expect the variant grid on the report whenever the data justifies it.** If an order contains two or more lines of the same configurable product template, the matrix is produced and printed. Previously you could disable this per order; now you cannot.
- **If you relied on suppressing the grid**, the only remaining lever is the report itself (report selection, or your own report customisation). Budget a short adaptation step if your printed purchase orders must never show a grid.
- **Do not re-create the field by hand.** A custom Boolean with the same name will not restore the old behaviour, because the reporting method no longer checks it.
- **Re-check your purchase order layout.** The former "Other Information" tab is now "Other Info", reorganised into Purchase, Billing and Tracking, and Payment Terms is on the main form. Anything you were used to finding in that tab has moved.
- **Data entry is unchanged.** Filling in quantities in the variant matrix, and the order of lines it generates, work exactly as in 19.0.

## What you gain by migrating

- **A cleaner purchase order form**: fewer technical options, a logically grouped "Other Info" tab, and Payment Terms where you actually use it.
- **More consistent printed documents**: any purchase order with several lines of the same configurable product always shows the variant matrix, so reviewers, approvers and vendors see the same breakdown without anyone having to remember to enable an option.
- **Simpler configuration**: one less field to explain, document and train on; grid control moves to the report, where document output belongs.
- **A supported, low-risk upgrade**: the addon keeps a single, unchanged reporting method, no data migration is required, and the only work is removing references to the deleted field from custom views or code.
