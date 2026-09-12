# stock migration guide (13.0 -> 14.0)

## What's new for users
The official release notes extract for this area is mostly about Point of Sale. The only stock-related item is:
- **PoS closing**: closing a PoS session now generates a single stock transfer instead of multiple transfers.

Other PoS items in the extract (cash rounding, loyalty, mobile, payments, product configurator, returns journal entries, scanning, UoM) are not stock addon features and are out of scope here.

## Technical data model changes
Based on the 13.0 -> 14.0 patches:

**Scheduling / dates refactor**
- Removed `stock.move.date_expected`. It was merged into `stock.move.date`, now labelled **Date Scheduled**. When a move is done, `date` still becomes the actual processing date.
- Added `stock.move.date_deadline` (**Deadline**): promise date coming from the top-level document (SO/PO). It propagates automatically to linked non-done/non-cancelled moves via the new `_set_date_deadline`.
- Added `stock.picking.date_deadline` (computed, stored) and `stock.picking.has_deadline_issue` (**Is late**).
- `stock.picking._order` changed from `date` to `scheduled_date`.
- `stock.picking.scheduled_date` now computes from `stock.move.date` instead of `date_expected`; `_set_scheduled_date` writes `date`.
- Removed from `stock.move` and `stock.rule`: `propagate_date`, `propagate_date_minimum_delta`, `delay_alert`. The old per-rule rescheduling propagation is gone; deadline propagation replaces it.
- `delay_alert_date` remains on `stock.move`; delay alerts are no longer configured per stock rule and are active independently.
- Stock rules now set/propagate `date` and `date_deadline`; procurement assignment order uses `priority desc, date asc`.
- No method signature changes were detected.

**Inter-company transit / reservations**
- `stock.location.should_bypass_reservation()` now also returns true for transit locations without a company (inter-company transit).
- `_action_confirm` on moves now calls `_action_assign` for confirmed moves whose source location bypasses reservation. This logic was removed from `stock.picking.action_confirm`.
- Push rules: a new move that should bypass reservation is set to `make_to_stock`; the move chain is not linked when the new move's source location bypasses reservation. Pull rules only set `move_dest_ids` when the source location does not bypass reservation.
- `stock.production.lot._product_qty` now counts transit quants only when the transit location has a company (i.e. owned transit stock).

## How your habits should change
The release notes extract only gives one direct stock/PoS habit change:
- If you use PoS, expect **one stock transfer per closed session**, not several.

It does not describe the stock date/reservation refactor in user terms. For those changes, use the technical section above: review scheduled dates, deadlines, and any custom rules or reports that still reference `date_expected`, `propagate_date`, `propagate_date_minimum_delta`, or `delay_alert`.

## What you gain by migrating
- **Simpler PoS-to-stock flow**: one transfer per session.
- **Clearer scheduling**: a single scheduled date plus a separate deadline for customer/vendor promises, with automatic deadline propagation.
- **Better lateness visibility**: picking-level `Is late` and move-level delay alerts.
- **Less obsolete configuration**: old reschedule/delay-alert fields on moves and rules are removed.
- **Safer inter-company transit**: receiving from another company no longer reserves the other company's stock, and broken chains avoid access errors.
- **Cleaner transit lot quantities**: only company-owned transit stock is counted.
