# stock migration guide (17.0 -> 18.0)

This guide covers the `stock` addon only. Changes listed below are the ones that affect day-to-day inventory usage and the data model. Some Inventory features announced for 18.0 belong to other addons (valuation, barcode, delivery carriers) or to the Enterprise edition, and are therefore not covered here.

## What's new for users

**Flexible routes: push rules now run at validation.** Push rules are triggered when a transfer is validated instead of when it is confirmed. Downstream transfers are no longer created in advance, so they stop cluttering your transfer lists, and you can deviate to another location at the last moment without leaving a "tail" of dead moves. All standard multi-step routes (2-step and 3-step receipts, pick/pack/ship, pick/ship) have been redesigned on this basis. Pure pull configurations remain possible.

**Split a transfer before validating it.** You can now create a backorder in advance, for example to deliver one transfer to two locations or on two dates ("Split" button).

**Reordering rules.** Minimum quantity greater than maximum is now blocked, auto-triggered rules can no longer be snoozed, and extra location filters are available.

**Returns.** When creating a return you can immediately add new products to send back in the same transfer.

**Next transfer button.** A smart button on a transfer shows the transfers that follow it in the chain.

**Detailed operations.** They are now reached through a dedicated button instead of being displayed inline.

**Cross-company lots/serials.** Lot and serial numbers remain valid through inter-company transfers.

**Empty locations.** The locations list now tells you which locations are empty.

**Putaway rules.** Incoming products are proposed in locations where the same product already is, or was last stored.

**Packages.** A package's location is now editable directly, including from the Kanban view.

**Tracking at delivery.** Products tracked only at delivery are shown as non-tracked at reception.

**Inventory adjustments.** Stock users can now apply inventory adjustments themselves.

**Local user preferences.** A default warehouse can be set per user.

## Technical data model changes

- `product.template`: `detailed_type` and `type = 'product'` are removed. A new stored boolean `is_storable` ("Track Inventory", default False) replaces them; `_compute_product_tooltip`, tracking computation and the write guards now depend on it. The `tracking` value `none` is relabelled "By Quantity".
- `stock.move`: `product_type` is replaced by a related `is_storable`; new `location_final_id` (the destination of the whole chain); `location_dest_id` is now a computed field based on the picking/picking type; new `_skip_push`, `_break_mto_link`, `_compute_location_dest_id`.
- `stock.picking`, `stock.picking.type`, `stock.move`: the `show_reserved` field (and `_compute_show_reserved`, `_enable_show_reserved`) is removed.
- `stock.picking`: `_create_backorder(backorder_moves)`, new `action_split_transfer`, `action_next_transfer`, `_get_next_transfers`, `_compute_show_next_pickings`, `action_detailed_operations`, `_compute_move_type` (internal transfers shipping policy), `_compute_bulk_weight`, `_compute_shipping_weight/volume`, `_package_move_lines`, `_can_return`, `action_put_in_pack(move_lines_to_pack)`; `_onchange_locations` and `_put_in_pack(move_line_ids, create_package_level)` signatures changed; `_compute_show_operations`, `_compute_show_qty_button`, `action_clear_quantities_to_zero` removed.
- `stock.picking.type`: new dashboard/kanban fields (`is_favorite`, `move_count`, graph data), `_search_display_name` replaces `_name_search`, `copy` replaced by `copy_data`.
- `stock.rule`: new boolean `location_dest_from_rule`; `_find_global_route` becomes `_find_or_create_global_route`; new company-consistency checks.
- `stock.location`: new `_child_of`, `is_empty` (computed and searchable), `name_create`, `copy_data`, `_unlink_except_master_data`; `copy` signature changed.
- `stock.lot`: `company_id` is now computed, `last_delivery_partner_id` computation dissociated, new `default_get`, `_read_group_location_id` loses the `order` argument, `copy` replaced by `copy_data`.
- `stock.quant`: removal-strategy helpers reworked (`_get_removal_strategy_order`, extra domain on `_get_quants_by_products_locations`), new `action_apply_all`, `check_lot_id`, `get_aggregate_barcodes`, `copy`; `action_inventory_at_date` removed.
- `stock.warehouse.orderpoint`: `_get_qty_to_order(force_visibility_days)`, `qty_to_order` computed on demand with search/inverse, `_check_min_max_qty`, new `create`.
- `stock.putaway.rule`: `_compute_storage_category`, last-used-location helpers.
