# project_purchase_stock migration guide (19.0 -> 20.0)

## What's new for users

This module is a small "glue" addon between Project, Purchase and Inventory. The 20.0 release notes we reviewed (Purchase, Project, Inventory, Accounting and localizations) contain **no entry describing this module's behaviour**: no new setting, no new screen, no new report. So nothing listed in the official release notes changes the way you use it, and we deliberately do not claim any Enterprise-only feature here.

What you will actually notice comes from the code change shipped in this version:

- Transfers (receipts and returns) generated from a purchase order that is linked to a project now carry that project in a more reliable way. The project is taken from the purchase order line behind each stock move, instead of being copied once when the transfer is first prepared.
- In the same release, confirming a purchase order with a **negative quantity** line now creates only the outgoing return transfer. Previously Odoo also created an unnecessary empty incoming transfer. Transfers are also kept in sync when you later update lines on an already confirmed purchase order.

Neither point adds a menu or a field you have to configure: it is a correction of the transfer/project linkage and of transfer creation.

## Technical data model changes

Changes are limited to model inheritance and one hook. There is **no new field, no removed field and no stored column to migrate**.

- **Removed:** the `purchase.order` extension (`models/purchase_order.py`). Its `_prepare_picking()` override, which injected `project_id` into the picking values at order confirmation time, is gone. The model `purchase.order` is no longer inherited by this addon.
- **Added:** a `stock.move` extension (`models/stock_move.py`) overriding `_get_new_picking_values()`, returning the standard values plus `project_id` taken from `self.purchase_line_id.order_id.project_id.id`.
- **Method hook moved:** `purchase.order._prepare_picking()` -> `stock.move._get_new_picking_values()`.
- **Behaviour consequence:** the project is resolved per stock move, from its purchase line's order. Any transfer created later, for instance after adding or updating lines on a confirmed order, therefore also receives the project. A transfer whose move has no purchase line simply gets no project.
- `stock.picking.project_id` itself is still provided by the `project_stock` module (a dependency), so no schema or data migration script is required.

## How your habits should change

- **Day-to-day usage: nothing to relearn.** No menu, no setting, no workflow step is added or removed, and there is no data to re-encode during the upgrade.
- **If you follow project stock:** keep checking the *Project* field on receipts and returns as you do today; it is now filled in more consistently, including for transfers created after the order was confirmed.
- **Returns from negative quantities:** you will simply see fewer transfers. If your team used to cancel or ignore the empty incoming transfer, that manual step is no longer needed.
- **If you have customisations:** any custom code, Studio automation or third-party module that relied on this addon's `_prepare_picking()` override on `purchase.order` must be reviewed and re-pointed to `stock.move._get_new_picking_values()`.
- For one transfer grouping moves from several purchase orders/projects, the project is taken from the purchase line of the move that triggers the transfer creation — worth a quick check in your first tests.

## What you gain by migrating

- **Cleaner project traceability:** receipts and returns follow the project of the purchase order, so stock and cost follow-up per project stays consistent without manual correction.
- **Less noise in Inventory:** no more empty transfers created when confirming purchase orders with negative quantities, and transfers stay aligned with later order line updates.
- **Lower maintenance:** the addon now uses a single, focused hook instead of extending another model, which means fewer breaks with future Odoo changes and far less custom code on your side.
- **Low migration effort:** no data migration, no reconfiguration, no retraining — the upgrade is essentially transparent for users, while giving you a supported and cleaner 20.0 base for the next years.
