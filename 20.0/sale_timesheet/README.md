# sale_timesheet migration guide (19.0 -> 20.0)

> The official 20.0 release notes extract covering this addon contained no
> `sale_timesheet`-specific entry, so this guide is based entirely on the
> 19.0 -> 20.0 code diff.

## What's new for users

**Services & Materials upselling view.** The main functional change is a new view on the
sale order that lets you record extra delivered services (flat fees) or consumable
products directly from the order:
- analytic lines are created from the sale order for those extras;
- the related sale order line is created or updated automatically, depending on the
  product's reinvoicing policy (`cost` creates a new line, `sales_price` updates an
  existing one);
- delivered quantities are recomputed when those analytic lines change;
- deleting the analytic line removes/reverts the sale order line effect.

Products whose reinvoicing policy is `no` stay manual-delivery only. These lines are
booked on the dedicated analytic plan of sale orders, so an analytic account is created
and set on the order; if a project is set on the order, its analytic account is used
instead.

**Clearer invoice ↔ timesheet link.** The *Timesheets* stat button on an invoice now
counts only real timesheets (lines attached to a project); service and material analytic
lines are no longer mixed into that count or duration.

**Reversed invoices.** Timesheet entries linked to a reversed invoice or credit note are
freed again when the reversal is created "with modification", instead of staying blocked,
so they can be re-invoiced.

**Timesheet portal link.** The portal link shown in the invoice e-mail template is now
displayed only when it is actually relevant.

## Technical data model changes

- `account.analytic.line.timesheet_invoice_id` is **renamed to `reinvoice_move_id`** (it
  is no longer timesheet-specific). Every filter, report or customization using the old
  name must be updated.
- `account.move.timesheet_ids` now filters on `project_id != False`; `timesheet_count`
  and `timesheet_total_duration` follow the same restriction.
- `account.analytic.line.order_id` moves from a stored related field to a computed one
  (`_compute_order_id`), fed by `so_line.order_id` on timesheet lines.
- The `so_line` field definition (domain, help) now lives in `sale`; `sale_timesheet`
  only extends its computation. New computes derive the timesheet `product_id` and
  `product_uom_id` from its sales order line.
- `product.template.expense_policy` is renamed `reinvoice_policy`
  (`_compute_visible_expense_policy` -> `_compute_visible_reinvoice_policy`).
- Removed: `_check_can_write`, `_unlink_except_invoiced` (replaced by
  `_restricted_fields_when_invoiced`, `_get_invoiced_line_write_error`,
  `_get_invoiced_line_delete_error`), `_link_timesheets_to_invoice`, `_get_range_dates`,
  `_timesheet_domain_get_invoiced_lines` and
  `sale.order.line._compute_qty_delivered_method`.
- Added / centralized: `account.move.action_post`,
  `_analytic_line_domain_get_invoiced_lines` (honours the `timesheet_start_date` /
  `timesheet_end_date` context), `account.move.reversal.reverse_moves(is_modify)`,
  `sale.order.line._get_delivered_quantity_by_analytic_domain` (keeps timesheets out of
  non-timesheet delivered quantities), `_is_line_reinvoicable`,
  `_prepare_qty_delivered`; `sale.order._create_invoices` signature becomes
  `(*args, **kwargs)`.
- `account.analytic.line.write` now resets `is_so_line_edited` when the project becomes
  non-billable.
- Project profitability panel helpers were moved out of `sale_timesheet` into a dedicated
  margin module.

## How your habits should change

- Treat timesheets and *Services & Materials* as two distinct flows: keep the timesheet
  grid for hours, use the new view for extra services and materials.
- Rebuild any saved filter, export or report based on the old `Timesheet Invoice` field
  onto `Reinvoice Move`.
- On products, the reinvoicing setting is now labelled **Reinvoice Policy**.
- Modified or posted invoices remain protected: existing rules about editing/removing
  already-invoiced timesheets still apply, with clearer error messages.

## What you gain by migrating

- Upsell extra services and materials without leaving the sale order, with delivered
  quantities and invoices staying consistent.
- Less manual cleanup: reversals, credit notes and deletions are handled automatically.
- Cleaner invoicing analytics, since timesheets are counted apart from other reinvoiced
  analytic lines.
