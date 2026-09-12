# point_of_sale migration guide (19.0 -> 20.0)

## What's new for users
No official release note extract matched this addon, so everything below comes from the 19.0 -> 20.0 code changes.

- **Daily invoicing**: a POS config can now post its sales and refunds entries every day ("Closing Mode: Daily" + a daily hour), instead of only when the session is closed.
- **Multi-currency payments**: customers can pay in a currency different from the order currency; conversion is automatic.
- **New QR payment flow**: payment methods gain an "external QR" type (Quick Pay / Bancontact Pay, new `pos_bancontact_pay` module), next to card terminals.
- **Simpler printing**: printers are configured in one place, several receipt printers are allowed, and the cash drawer setting moved onto the printer. Epson Server Direct Print is dropped in favour of Local Network Access.
- **Edited orders are always tracked** in the order chatter (no option to enable).
- **Lighter, faster POS**: the POS no longer needs the Inventory app for simple shops, rescue sessions are gone (late orders re-sync to the open session), and data loads incrementally so the till starts faster.

## Technical data model changes
**Models removed**: `pos.pack.operation.lot`, `pos.printer.pending.receipt`, `product.template.attribute.exclusion` (replaced by `excluded_value_ids` on `product.template.attribute.value`), and the `pos.load.mixin` inheritance on `ir.module.module`. **Models added**: `pos.order.receipt` (mixin), `account.move.line.pos_order_line_id`, `account.bank.statement.pos_session_id`.

**pos.config**: removed `invoice_journal_id`, `pos_invoice_journal_id`, `split_transactions`, `picking_type_id`, `warehouse_id`, `route_id`, `picking_policy`, `ship_later`, `printer_ids`, `default_receipt_printer_id`, `epson_printer_ip`, `proxy_ip`, `iface_print_via_proxy`, `iface_scan_via_proxy`, `iface_electronic_scale`, `iface_cashdrawer`, `use_epson_server_direct_print*`, `number_of_rescue_session`, `last_session_closing_cash`, `order_edit_tracking`. Renamed to `use_*`: `use_order_printer`, `use_posbox` (then removed), `use_header_or_footer`, `use_closing_entry_by_product`; `is_kiosk_mode` -> `use_kiosk_mode`. Added: `default_partner_id` (required), `current_cash_register_balance`, `session_closing_mode`, `session_closing_daily_hour`, `preparation_printer_ids`, `receipt_printer_ids`, `preparation_devices`.

**pos.session**: `sales_move_id`/`refunds_move_id` -> `sale_move_ids`/`refund_move_ids` (One2many, multiple entries per session), `correction_move_ids` is now One2many, `sale_move_count`/`refund_move_count` added, cash register fields and `rescue` removed.

**pos.payment**: `currency_rate` is no longer related; `amount_currency`, `foreign_currency_id` and `qr_code` added. **pos.payment.method**: `use_payment_terminal` -> `payment_provider`, `type` gains `external_qr`, `qr_code` method type renamed `bank_qr_code`, `currency_ids` added. **pos.printer**: `use_type`, `use_lna`, `use_cashdrawer`.

**Other**: `pos.order.is_invoiced` -> `is_singly_invoiced` / `is_globally_invoiced`; the POSS journal is now a *Sales* journal; prohibited tax writes also trigger on draft orders. Method signatures changed: `_load_pos_data_domain(data)` (config argument dropped), `load_data(local_data)`, `_create_payment_line(..., foreign_currency, amount_currency)`, `_prepare_refund_data(refund_order)`, `action_send_receipt(email)`, `_compute_amount_line_all(qty=None)`.

## How your habits should change
- Set a **Default Customer** on every POS config: the receivable account for session closing now comes from that partner.
- Check the **Point of Sale Journal** (must be a Sales journal) — invoices now use it too, so the separate invoice journal is gone.
- Only **one cash payment method per config**; cash counting happens on the linked bank statement, not on the session.
- Payment splitting only applies to **Customer Account (pay_later)**; paying by customer account always creates an invoice, and "Settle Due" now settles invoices only, never POS orders.
- Session closing produces **one sales entry and one refunds entry** (possibly one pair per day in Daily mode) instead of a single move.
- Configure printers in the POS Printer menu, tag each as Receipt or Preparation, and pick the default receipt printer from the POS itself; cash drawer opening is per printer.
- IoT Box setup is no longer available in Community: use LNA/ePOS printers, or Enterprise IoT.
- Install the `pos_stock` bridge if you still need deliveries and lot tracking.

## What you gain by migrating
