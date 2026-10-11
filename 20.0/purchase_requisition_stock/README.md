# purchase_requisition_stock migration guide (19.0 -> 20.0)

## What's new for users

This addon is small, and in 20.0 it becomes smaller still: it now covers only the link between a purchase requisition (purchase agreement / blanket order) and the stock operation type used when the purchase order is created. Everything related to purchase alternatives now lives in a dedicated module, **purchase_alternative_stock**.

Functional changes worth knowing about in 20.0 (Community edition):

- **Alternatives comparison**: the comparison screen used to choose the best offer has been improved and made simpler to read. This functionality is no longer shipped by `purchase_requisition_stock` but by `purchase_alternative_stock`, which depends on both `purchase_alternative` and `purchase_stock`. Users of requisitions with alternatives therefore see the same feature under a new, clearly named module.
- **Purchase agreement structure**: sections and notes can now be added in purchase agreements / blanket orders, exactly as already possible on purchase orders. This makes long negotiated agreements much easier to organise and read.
- **Expected arrival date**: the expected arrival date can be edited directly on the purchase order while the original vendor-provided date is retained, so on-time delivery indicators stay accurate. A one-click action also lets you recompute expected arrival dates when the order deadline is already in the past.
- **On-time delivery (OTD) tracking** on purchase orders and order lines is consequently no longer provided by this addon (see technical section below); it is provided by the alternative-stock module.

The release notes do not document any other change specific to requisitions feeding stock. Items such as vendor quality rate or portal/Incoterm improvements are not part of this addon and, where they rely on Enterprise apps, are intentionally not presented here.

## Technical data model changes

Source of truth: the 19.0 -> 20.0 code delta.

Removed from `purchase_requisition_stock`:

- `purchase.order.on_time_rate_perc` — `fields.Float(string="OTD")`, computed field (`compute="_compute_on_time_rate_perc"`, depends on `on_time_rate`). Removed together with its compute method `_compute_on_time_rate_perc`.
- `purchase.order.line.on_time_rate_perc` — `fields.Float(string="OTD")`, `related="order_id.on_time_rate_perc"`. Removing it also removed the `PurchaseOrderLine` inheritance class from this addon's `models/purchase.py`.

Kept unchanged:

- `purchase.order._onchange_requisition_id()` — still calls `super()`, and when `requisition_id` is set it copies `picking_type_id` from the requisition. No signature change.

Net effect: 3 deletions, 0 additions in this module. No new model, no renamed field, no change to stored data of this addon. The OTD percentage fields and their computation were relocated to the new **purchase_alternative_stock** module, which brings together purchase-alternative and purchase-stock functionality. Upgrading is therefore safe for requisition data, but any custom view, report, filter or server action of yours that reads `on_time_rate_perc` on a purchase order or order line must either be updated or kept working by installing `purchase_alternative_stock`.

## How your habits should change

- If you displayed the "OTD" percentage on quotations, purchase orders or their lines, install `purchase_alternative_stock` on 20.0; otherwise that column disappears from your screens. The underlying rate is still recorded, it simply comes from another module.
- Keep using requisitions / blanket orders as before: choosing a requisition on a purchase order still sets the receipt operation type automatically.
- Organise long agreements with the new sections and notes instead of splitting them into many agreements.
- Use the editable expected arrival date together with the original vendor date when you need to explain a late delivery; use the recompute action when confirmation happened after the deadline.
- If you maintain custom code on this addon, review it for references to the removed model `purchase.order.line` inheritance before upgrading.

## What you gain by migrating

- **Clearer module boundaries**: alternative-sourcing logic no longer clutters the requisition-to-stock link, which means fewer surprising side effects on purchase orders.
- **Same features, better home**: the improved alternatives comparison screen and OTD figures remain available through `purchase_alternative_stock`, with installable dependencies that reflect real responsibilities.
- **Better agreements**: sections and notes in blanket orders make negotiated terms readable for buyers and approvers.
