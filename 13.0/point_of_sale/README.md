# point_of_sale migration guide (12.0 -> 13.0)

13.0 reworks POS cash control, plugs the POS into the merged accounting engine, and shares pricelist settings with Sales. Here is what changes for Community users moving from 12.0.

## What's new for users

- **Cash Control.** A cashbox is now a reusable *template*, shareable by several POS configurations. At session opening the counted content comes from the last closed session, like a real cashbox; you can reload the default cashbox at start, and cash differences are tracked.
- **Session closing.** A single accounting entry is created when the session closes, improving performance and making the session's payment history clearer.
- **Reporting.** POS sales now count into product sales, and POS reporting handles multi-currency.
- **Employees.** One session can be shared by several employees connected at the same time.
- **Usability.** A stat button on the POS config lists the session's orders, menus were reordered, default picking types ease on-boarding, the frontend has a keypad, and a next-activity alert reminds you to close/reopen the session.

*Note: payment-terminal integrations (SIX, Adyen) and other Enterprise items of the 13.0 release notes are not part of this Community addon.*

## Technical data model changes

Cashbox:
- `account.cashbox.line` loses `default_pos_id`; its POS inheritance is removed.
- `account.bank.statement.cashbox` gains `pos_config_ids`, `is_a_template` and a `currency_id` computed from the POS config.
- `pos.config.default_cashbox_lines_ids` → `default_cashbox_id` (Many2one on `account.bank.statement.cashbox`).
- New computed `last_session_closing_cashbox`; `last_session_closing_cash` is 0 when the last session had no cash register.
- `cash_control` can no longer be changed while a session is open (new constraint).
- `pos.session.open_cashbox()` renamed `open_cashbox_pos()`; new `set_default_cashbox()` and `_validate_cashbox()` (moves `new_session` to `opening_control`).

Accounting merge:
- `account.invoice[.line/.tax]`, `account.voucher[.line]` and the `account_voucher` module are gone: everything is `account.move` / `account.move.line`.
- `pos.order.invoice_id` is removed; the invoice is now an `account.move` on `account_move`, and the invoice action opens `account.move` (`out_invoice`/`out_refund`).
- `_prepare_invoice()` and `_action_create_invoice_line()` become `_prepare_invoice_line(order_line)`; `_prepare_analytic_account()` disappears, so analytic accounts are no longer set on POS journal items by that hook.
- `_reconcile_payments()` no longer includes invoice move lines; anglo-saxon pricing uses `_stock_account_get_anglo_saxon_price_unit()`; tax groups use `flatten_taxes_hierarchy()`.

Other:
- `pos.category`: `image` and `image_64` removed, `image_128` becomes a real `fields.Image` (128x128).
- `res.config.settings`: `pos_sales_price` and `pos_pricelist_setting` removed — pricelists are now configured with Sales.

## How your habits should change

- POS "invoices" are journal entries: the menus and reports that listed invoices now show journal entries (merged Accounting engine).
- Expect one journal entry per session at closing instead of per-payment entries.
- Review your POS configurations: old default balance lines are replaced by a default cashbox template.
- Configure pricelists once, for Sales and POS, in the shared pricelist settings.

## What you gain by migrating

- Cheaper, clearer session closing with a single auditable accounting entry.
- Cash handling that matches reality (templates, carry-over, tracked differences).
- Reliable POS turnover in product sales reporting, multi-currency included.
- Several employees per session, keypad entry, faster on-boarding.
- A POS aligned with the modern accounting engine, easier to reconcile and extend.
