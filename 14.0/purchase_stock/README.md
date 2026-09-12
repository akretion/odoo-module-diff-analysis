# purchase_stock migration guide (13.0 -> 14.0)

## What's new for users

Most 14.0 changes affecting this addon come from the Inventory app:

- **Availability dates** on transfers, manufacturing orders and sales orders: each document shows the expected date needed to fulfil the order.
- **Reordering rules**: forecasted quantity is checked at specific dates, taking security days and lead times into account.
- **Replenishment report and forecasted inventory**: see what needs replenishing and create manual reordering rules.
- **MTO route replaced** by a more flexible, automated replenishment approach.
- **Sales ⇄ Purchase traceability**: the PO generated automatically from a SO is linked and visible on both documents.
- **Product description feedback**: descriptions updated on a Sales Order follow through to the generated Purchase Orders.
- On the Purchase side, when no vendor pricelist line matches, the product cost is used as purchase price.

Note: several 14.0 Purchase highlights (Approvals-based purchase requests, OCR bill recognition) are Enterprise features and are not covered by this Community guide.

## Technical data model changes

One upstream commit only: "stock*, mrp*: Dates Refactor". No model added or removed; three fields removed on `purchase.order.line`:

- `delay_alert` (Boolean)
- `propagate_date` (Boolean)
- `propagate_date_minimum_delta` (Integer)

Behavior changes:

- `stock.move.date_expected` is merged into `date`. Scheduled dates are no longer propagated; only the deadline is.
- A new `date_deadline` drives the promise made to/by the vendor and is read-only on pickings and moves.
- On purchase picking lines, `date` now holds the planned date (`date_planned`) instead of the order date; `date_deadline` = planned date + company purchase lead time (`po_lead`). This inverts the sales logic, because a vendor promise may slip.
- `_update_move_expected_date()` becomes `_update_move_date_deadline()` and writes `date_deadline = new_date + po_lead` instead of shifting `date_expected` above a minimum delta.
- Moves are sequenced by `date` instead of `date_expected`.
- `delay_alert` is no longer driven by stock rules: delay alerts now apply in all cases.
- Stock rules no longer pass `delay_alert`, `propagate_date` or `propagate_date_minimum_delta`, and these keys are removed from procurement merge/sort keys.
- Warehouse "Buy" rules no longer set `propagate_date`; only `propagate_cancel` remains.

## How your habits should change

- The "Delay alert", "Propagate Rescheduling" and "Reschedule if Higher Than" checkboxes are gone from supplier lines and buy rules. Delay alerts now always apply.
- Rescheduling is no longer pushed from move to move. What you maintain is the **deadline** (vendor promise + purchase lead time); scheduled dates follow their own document.
- Changing the planned date of a confirmed PO line updates the deadline of its not-yet-done moves directly, with no minimum-delta threshold.
- The picking `date` field no longer mirrors the order date: look at the availability/deadline dates on pickings, MOs and SOs.
- On completed moves, the previous scheduled date is no longer kept — the date becomes the processing datetime.

## What you gain by migrating

- One coherent date model (deadline + scheduled date) across purchase, stock and manufacturing: easier to explain, filter and report on.
- Reliable vendor promises: receipts are planned with the purchase security lead time, matching replenishment.
- Fewer fields to configure — delay alerts work out of the box.
- Better forecasting and replenishment tooling, plus clear SO ↔ PO traceability.
- Staying on 13.0 keeps a deprecated rescheduling mechanism that Odoo no longer maintains.
