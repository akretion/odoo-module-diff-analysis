# repair migration guide (16.0 -> 17.0)

Odoo 17 reworks the Community Repair app: repair orders are decoupled from invoicing and now driven by stock moves, with a Sale Order binding.

## What's new for users

- **Repairs and Sales/Invoicing are now separate scopes.** Invoicing no longer happens from the repair order; parts and fees are invoiced through a linked Sale Order. A Sale Order can carry several Repair Orders, and a Repair Order links back to the sale line that created it.
- **Create repair orders from sale orders, and quotations from repair orders.** Flag a product "Create Repair" and a Repair Order is generated when the sale order is confirmed; from a Repair Order you can create the linked quotation, then jump to it from the Sale Order (repair count / button).
- **Component reservation and replenishment from the Repair module**: reserve / unreserve buttons, "set quantities to reservation", clear quantities, and a Component Status (Available / Expected / Late, with expected date) to check readiness before repairing.
- **New 'Recycle' repair line type** alongside Add and Remove, to return previously consumed components into stock.
- **Dedicated "Repairs" operation type per warehouse**, with its own sequence (e.g. `WH/RO/00001`), default source / destination / removed-parts / recycled-parts locations, and counters for Repair Orders to process, Confirmed and Under Repair.
- **Under Warranty** checkbox: transferred products are priced at 0. Scheduled date is now a date and time, and repair orders have a Responsible and Priority.

The release-notes "Industries" section does not concern this addon.

## Technical data model changes

Models removed: `repair.line` and `repair.fee` are replaced by an inheritance of `stock.move` (`repair_id`, `repair_line_type` = add / remove / recycle). `account.move` and `account.move.line` no longer inherit repair fields (`repair_ids`, `repair_line_ids`, `repair_fee_ids` removed).

`repair.order`: removed `invoice_method`, `invoice_id`, `invoice_state`, `invoiced`, `repaired`, `pricelist_id`, `currency_id`, `partner_invoice_id`, `address_id`/`default_address_id`, `quotation_notes`, `guarantee_limit`, `amount_untaxed/_tax/_total`, `tax_calculation_rounding_method`, `operations`, `fees_lines`, `allowed_picking_type_ids`. State selection: `ready` ("Ready to Repair") and `2binvoiced` ("To be Invoiced") removed; `draft` relabelled "New". Added: `picking_type_id`, `procurement_group_id`, `location_dest_id`, `parts_location_id`, `recycle_location_id`, `move_ids` (Parts), `parts_availability`, `parts_availability_state`, `is_parts_available`, `is_parts_late`, `under_warranty`, `sale_order_line_id`, `repair_request`, `reserve_visible`/`unreserve_visible`, `show_set_qty_button`/`show_clear_qty_button`. `schedule_date` is now a required `Datetime`; `location_id` is relabelled "Location"; `picking_id` only accepts return transfers; `product_id` is filtered against the return's products.

Other models: `stock.picking.type` gains `code = 'repair_operation'` plus new default-location fields, and counters `count_repair_ready`, `count_repair_confirmed`, `count_repair_under_repair`; `stock.warehouse.repair_type_id`; `product.template.create_repair`; `stock.lot.repair_order_ids` is now computed from stock moves; `sale.order.repair_order_ids` / `repair_count` and `sale.order.line` create or cancel repair orders, delivered quantity being taken from done repair moves.

Methods: `action_repair_confirm` becomes `_action_repair_confirm`; `action_repair_end` checks done quantities (opens an "Uncomplete Move(s)" wizard) before `action_repair_done`; `action_repair_ready`, `action_repair_invoice_create`, `_create_invoices`, `action_created_invoice`, `action_send_mail` and `button_dummy` are removed; new `action_assign`, `action_unreserve`, `action_set_quantities_to_reservation`, `action_clear_quantities_to_zero`, `action_create_sale_order`, `action_view_sale_order`. No public method signature change was reported.

## How your habits should change

- Don't look for an invoice on the repair order: quote and invoice through the Sale Order. Invoice Method, pricelist, currency and repair totals are gone.
- Workflow is now New → Confirmed → Under Repair → Repaired. "Ready to Repair" and "To be Invoiced" no longer exist.
- Parts are stock moves: use Add / Remove / Recycle, reserve, adjust done quantities and watch Component Status rather than repair line prices and taxes.
- Handle warranty with the "Under Warranty" checkbox, not a warranty expiration date.
- Check each warehouse has a Repairs operation type with suitable default locations. "Create Repair Orders from Returns" is now off by default and only offered where returns exist.

## What you gain by migrating

