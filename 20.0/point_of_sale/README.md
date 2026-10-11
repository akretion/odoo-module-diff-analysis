# point_of_sale migration guide (19.0 -> 20.0)

## What's new for users

- **Simplified inventory**: Point of Sale no longer depends on the Inventory app. Without it, POS only increases/decreases a single on-hand quantity per product. Complex stock operations (pickings, lots, valuation) move to the new `pos_stock` bridge module (`pos_sale_stock` for sales orders).
- **Multi-currency payments**: a customer can pay in a currency other than the order currency; amounts are converted with the order rate.
- **Daily accounting**: new Closing Mode (Daily / At closing) with a daily hour. In Daily mode, the session's invoices and journal entries are generated automatically each day, even while the session stays open.
- **Payment methods**: new "Quick Pay (QR Code)" type with a Payment Provider field; the terminal setting becomes "Automatically validates orders paid with an electronic payment (terminals, qr codes, ...)". Bancontact Pay is supported via `pos_bancontact_pay`.
- **Printers**: several receipt printers per POS, preparation vs receipt types, one ticket per product, printer test buttons, cash drawer linked to a printer, manual paper size. IoT Box and Epson Server Direct Print support are removed from Community.
- **Receipts**: a single QWeb receipt template shared by backend and frontend; receipts can be printed/downloaded from the order.
- Snooze products, drag & drop product reordering, order search by invoice/receipt number.

## Technical data model changes

- **pos.session**: closing now creates two aggregated moves (sales, refunds); `sale_move_ids` / `refund_move_ids` / `correction_move_ids` are One2many. Cash fields (`cash_register_balance_start/end/end_real`, `cash_journal_id`, `cash_register_difference`, `cash_real_transaction`) are gone — cash flows through `account.bank.statement(.line)`. Also removed: `invoice_journal_id`, `split_transactions`, `is_cash_count`, `is_invoiced`, `session_move_id`, `is_online_payment`, settle/deposit products, `rescue`.
- **pos.config**: new required `default_partner_id`, plus `current_cash_register_balance`, `session_closing_mode`, `session_closing_daily_hour`; `journal_id` is restricted to sale journals and the auto-created POSS journal becomes type *sale*.
- **pos.order / line**: picking fields removed (`picking_ids`, `picking_count`, `failed_pickings`, `picking_type_id`, `stock_reference_ids`, `shipping_date`, warehouse/route/policy fields), and the `pos.pack.operation.lot` model plus `pack_lot_ids` disappear; `is_invoiced` is replaced by `is_singly_invoiced` / `is_globally_invoiced`.
- **pos.payment(.method)**: `foreign_currency_id` and `amount_currency` on payments; `currency_ids`, `payment_provider` (replaces `use_payment_terminal`), `auto_validate_terminal_payment` → `auto_validate_electronic_payment`; type `qr_code` → `bank_qr_code`.
- **Printers / misc**: `printer_ids` → `preparation_printer_ids` + `receipt_printer_ids`; `epson_printer_ip` and the cash drawer option move to `pos.printer`; IoT fields (`proxy_ip`, `use_posbox`, `iface_*_via_proxy`, `iface_electronic_scale`) removed; booleans renamed to `use_*` (`use_order_printer`, `use_closing_entry_by_product`, ...).
- **Other models**: `product.template.attribute.exclusion` removed (`excluded_value_ids` on attribute values); `account.move.line.pos_order_line_id` added; cash rounding cannot be changed while a session uses it; taxes used by open/draft POS orders can no longer be modified or archived.
- **Data loading**: `_load_pos_data_domain(data)` no longer takes `config` (use `data['pos.config']`), new `_load_pos_data_dependencies()`, `load_data(local_data)` enables incremental sync by `write_date`; receipt templates load as `ir.ui.view` (no more `ir.module.module`).

## How your habits should change

- Check cash through the cash control / bank statement, not the session form.
- "Settle due" only settles invoices: paying on customer account always creates an invoice.
- Splitting a payment is only possible with the customer-account method.
- One invoice journal (the POS journal) and one mandatory default customer per configuration.
- Configure receipt/preparation printers in the Printers menu, with one cash method per POS.
- Order edits are always logged in the chatter; rescue sessions are gone (orders retry the current session).

## What you gain by migrating

- Accounting that reconciles itself: aggregated sale/refund entries, daily invoicing without closing, payments posted directly on the move.
- A lighter POS: no Inventory app needed for simple shops, full stock behaviour kept via `pos_stock`.
- Faster, incremental loading of POS data to devices.
- Multi-currency checkout and an extensible QR payment type.
- Better receipt/printer handling and standardized, cleaner settings.
