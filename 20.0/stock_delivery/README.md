# stock_delivery migration guide (19.0 -> 20.0)

## What's new for users

Nothing functional changes in **stock_delivery** between 19.0 and 20.0. The two options this addon adds on the operation type are still there, with the same wording:

- **Auto Print Carrier Labels** — the carrier labels of a transfer are printed automatically when they are created.
- **Auto Print Export Documents** — the export documents are printed automatically when they are created (availability depends on the carrier and the destination).

The official 20.0 release notes contain no item describing a change in `stock_delivery` itself. The Inventory notes do list delivery-side improvements: a CMR document downloadable from a transfer and prefilled from it, Sendcloud pickup points selectable (and correctable) from a sales order or transfer, the package reference sent to Sendcloud, and a new ShipStation connector. These come from the shipping-connector modules that sit on top of this addon, not from `stock_delivery` itself, and should not be expected from this upgrade alone. Nothing Enterprise-only is claimed here.

## Technical data model changes

Ground truth (20.0 source, PR odoo/odoo#282884):

- New file `models/stock_picking_type.py` holding the `StockPickingType` class (`_inherit = "stock.picking.type"`), declared in `models/__init__.py`.
- That class was removed from `models/stock_picking.py`.
- No model added, removed or renamed. No field added, removed or renamed: `auto_print_carrier_labels` and `auto_print_export_documents` are both `Boolean` fields with identical labels and help texts.
- No field behaviour change, no method signature change.
- Cleanup only: `stock_picking.py` no longer imports `SUPERUSER_ID`.

This is a pure code-organisation refactor ("each inherited model in its own file", per the Odoo coding guidelines). The database schema is untouched, so the standard `-u stock_delivery` upgrade applies with no data migration. The `odoo/upgrade` scripts referenced by the PR carry no data change for this module. Custom Python that reached `StockPickingType` through the `stock_picking` file (imports or monkey patches) must be repointed; XML view inheritance, settings and user-facing configuration are unaffected.

## How your habits should change

- **Functional users:** no change at all. Same settings, same transfer screens, same label and document printing flow, nothing to re-train.
- **Developers / integrators maintaining customisations:** look for `StockPickingType` overrides written in `stock_picking.py` and move or re-target them to `stock_picking_type.py`. Any code importing the class from `odoo.addons.stock_delivery.models.stock_picking` breaks in 20.0 — use the model registry (`self.env['stock.picking.type']`) instead.
- **Testers preparing a regression run:** no new scenario is needed. A quick check that the two auto-print options are still visible on the operation type and still trigger printing is enough.

## What you gain by migrating

For this addon alone, the gain is code quality rather than features:

- `stock_delivery` now follows the same file layout as `stock`, so reading and debugging the two is consistent and less error-prone (no more `StockPicking` and `StockPickingType` mixed in one file — a real source of mistakes when patching).
- Cheaper, safer custom development and future upgrades: your overrides live where the coding guidelines say they should.
- A strictly low-risk upgrade path: no schema change, no migration script, no field rename, minimal regression surface on carrier labels and export documents.
- Staying on a supported Odoo version, with the surrounding delivery and shipping-connector improvements of 20.0 available to the modules that provide them.
