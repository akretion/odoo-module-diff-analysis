# stock migration guide (12.0 -> 13.0)

## What's new for users

**Putaway rules, rebuilt for usability.** Instead of creating a "Put Away Strategy" record and attaching it to a location, you now define putaway rules directly on locations: each rule targets a product *or* a product category, states where the product arrives, and where it must be stored. Rules are reachable from the product and product category forms (stat buttons) and from a new Product configuration menu entry.

**Inventory adjustments, simplified.** The "Inventory of..." filter is gone. Create an adjustment, optionally restrict it to one or more locations and/or one or more products — or leave both empty to count all your stock. Lines now live in their own list view, with a colour legend (outdated quantities in red, differences in blue, newly added lines in bold), a Difference column and "Counted Quantity" wording. You can refresh lines whose on-hand quantity became outdated, and validated adjustments expose their accounting moves through a stat button.

**Rescheduling propagation is now per rule.** The "No Rescheduling Propagation" setting disappeared from Inventory settings. Whether a date change is pushed to the next move — and the minimum delta above which it is pushed — is now defined on each operation rule and copied to the moves it generates.

**Reserved quantities are always visible.** The "Show Reserved" option on operation types is removed: detailed operations always display reserved lines (lot/serial, source location, package).

Other user-visible points: the Make To Order route is renamed **Replenish on Order**; product descriptions on transfers can differ per transfer type (receipt, delivery, internal); stock move lines carry a company (multi-company); reordering-rule counters on products are computed per company.

## Technical data model changes

- **Removed:** `stock.picking.type.show_reserved`, `stock.move.move_line_nosuggest_ids` (and `_get_move_lines`), plus the `view_stock_move_nosuggest_operations` view.
- **Putaway:** models `product.putaway` and `stock.fixed.putaway.strat` are removed, replaced by `stock.putaway.rule` (product_id, category_id, location_in_id, location_out_id, sequence). `stock.location.putaway_strategy_id` becomes `putaway_rule_ids`; `get_putaway_strategy()` is renamed `_get_putaway_strategy()`. New `putaway_rule_ids` on product (variant), product category and location.
- **Rescheduling:** `res.company.propagation_minimum_delta`, `res.config.settings.propagation_minimum_delta` and `use_propagation_minimum_delta` are removed. Added `propagate_date` and `propagate_date_minimum_delta` on `stock.rule` (default 1) and `stock.move`.
- **Inventory adjustment:** `stock.inventory` loses `location_id`, `product_id`, `package_id`, `partner_id`, `lot_id`, `category_id`, `filter`, `exhausted`, `total_qty`, `inventory_location_id`; it gains `location_ids` and `product_ids` (many2many) and `start_empty`. `stock.inventory.line` loses `product_uom_category_id`, `theoretical_qty` is no longer stored, and `difference_qty`, `inventory_date`, `outdated`, `is_editable` are added; "Checked Quantity" becomes "Counted Quantity". New methods: `action_open_inventory_lines`, `action_view_related_move_lines`, `action_refresh_quantity`. Validation now requires the Stock Manager group.
- **Reordering counters:** `nbr_reordering_rules`, `reordering_min_qty` and `reordering_max_qty` (product and product template) are computed per company (`compute_sudo=False`).

## How your habits should change

- Operation types: nothing to tick anymore — detailed operations always show reserved quantities.
- Putaway: re-create your old strategies as location putaway rules; the sequence keeps category priority.
- Inventory adjustments: no more filters. Select locations/products or count everything, edit only counted quantities, refresh outdated lines, and remember that only a Stock Manager can validate. Cancelled adjustments keep their lines instead of regenerating them.
- Rescheduling: set propagation behaviour on each rule rather than once in settings.

## What you gain by migrating

- Putaway rules that match real warehouse practice and are visible where products are configured.
- Much faster, safer inventory counting, with difference and outdated feedback instead of blind entry.
- Per-rule rescheduling control, so MTO chains no longer shift dates for trivial changes.
- Consistent multi-company visibility, correct reordering counters, clearer transfer descriptions and a simplified detailed-operations screen.

*Note: one large multi-company patch was outside the review budget, so this list may not be exhaustive.*
