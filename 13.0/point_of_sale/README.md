# point_of_sale migration guide (12.0 -> 13.0)

## What's new for users
* **One accounting entry per session** - closing a PoS session now creates a single journal entry instead of one entry per order.
* **Cash control** - set a default cash opening/closing and track any cash difference at session start and end.
* **Multiple employees per session** - several employees can sell simultaneously in the same session.
* **Keypad** - use the keypad on the PoS frontend to manage orders.
* **Usability** - new stat button to reach order details, reordered menus, onboarding with default picking types.
* **Alerts** - a next activity warns you when a session should be closed and reopened.
* **Reporting** - PoS sales are counted in product sales, and PoS reporting supports multiple currencies.
* The accounting "engine" merge (customer invoices and journal entries now share one model) is visible in PoS when invoicing an order.

Terminal integrations (SIX, Adyen) and IoT ticket printers are not part of the Community edition; confirm them with your integrator.

## Technical data model changes
New models
* `pos.payment.method` and `pos.payment` replace `account.journal` (and `account.bank.statement` / `account.bank.statement.line`) as payment methods and payments.
* `pos.payment.method` carries `receivable_account_id`, `is_cash_count`, `cash_journal_id`, `company_id`, `config_ids`, `open_session_ids`. A default Cash and Bank method are auto-created per company using the new `res.company.account_default_pos_receivable_account_id`.

Changed fields
* `account.journal`: `journal_user` removed, `pos_payment_method_ids` added.
* `pos.config`: `journal_ids` -> `payment_method_ids`; `group_by` removed; `default_cashbox_lines_ids` -> `default_cashbox_id` (cashbox template); `last_session_closing_cashbox` added; writing a config with an open session now raises a UserError.
* `pos.session`: `statement_ids` -> `payment_method_ids` (related to the config).
* `pos.order`: `payment_ids` added; `invoice_id` removed, `account_move` now holds the invoice/journal entry, since `account.invoice` was merged into `account.move`.
* `pos.category`: `image` / `image_64` / `image_128` -> a single `image_128`.
* `res.config.settings`: `pos_sales_price` and `pos_pricelist_setting` removed (pricelists now share the Sales configuration).

Methods
* `_payment_fields(ui_paymentline)` -> `_payment_fields(order, ui_paymentline)`.
* `_match_payment_to_invoice` removed.
* Per-order account move creation removed from `pos.order`: the entry is produced once per session at closing.
* `action_pos_order_invoice` builds an `account.move` with `invoice_line_ids` (no `account.invoice`), and posts it.

## How your habits should change
* Configure payment methods under Point of Sale > Payment Methods, not on journals. The "Use in Point of Sale" checkbox on journals is gone.
* Each payment method owns its receivable account; this is what allows the single session entry. Cash lines are reconciled automatically, non-cash receivable lines are combined per method and can be reconciled with the reconciliation widget.
* "Group Journal Items" is gone.
* Cash control uses reusable cashbox templates: a session starts with the content of the last closed session, and you can reset it to the config default. You cannot change the cash control setting or the config while a session is open.
* Invoices issued from the PoS are journal entries; open them from `account.move`.
* Pricelists are configured once for Sales and PoS.
* Product categories keep only one image field.

## What you gain by migrating
* Far faster closing of sessions with thousands of orders, because journal entries are no longer created order by order.
* Cleaner, easier-to-audit accounting: one entry per session, cash automatically reconciled, and other payments grouped by payment method in a single receivable line.
* A real payment history: payments are dedicated records, fully searchable and reportable.
* Better cash management with default cashboxes and cash difference tracking.
* You stay on a supported version, with the accounting improvements of 13.0 (invoice/journal entry unification, reconciliation, reporting).
