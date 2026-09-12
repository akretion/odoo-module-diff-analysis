# point_of_sale migration guide (14.0 -> 15.0)

## What's new for users

- **Pay Later / Customer Account payments**: customers can buy on credit. A "Customer Account" payment method ships by default. If the order is invoiced, the customer's due appears as soon as the order is synced; if not invoiced, it appears when the session is closed.
- **Paid invoices really look paid**: POS now reconciles invoices with the registered payments right after the order is synced, so the invoice PDF and the backend both show it as paid.
- **Partial payments**: customers can pay part of an order; the invoice states the correct remaining due.
- **Opening and closing cash control in the POS UI**: a new money calculator (coins/bills) to count cash, a closing popup summarising orders, payments, cash moves, and an option to set a maximum allowed difference.
- **Better session follow-up**: session chatter logs opening/closing details, and the "Journal Items" button on the session always shows the invoices and their payment moves.
- **Product packaging in POS**, "ship later" option, finding ongoing and paid orders on the same screen, session loading limits (products/partners loaded at opening, the rest in the background), more secure session login, and assorted usability improvements.

## Technical data model changes

**New model**: `pos.bill` (Coins/Bills).

**pos.payment.method**: `cash_journal_id` replaced by `journal_id` (cash or bank journal). New computed-stored `is_cash_count`, new `type` selection (`cash`, `bank`, `pay_later`), new `outstanding_account_id`. `receivable_account_id` is no longer required and `split_transactions` is renamed "Identify Customer".

**pos.payment**: new `account_move_id`.

**pos.session**: new `bank_payment_ids` (linked to `account.payment`) and `opening_notes`. Removed `open_cashbox_pos`, `_warning_balance_closing` and the `closing.balance.confirm.wizard` model. New methods `close_session_from_ui`, `post_closing_cash_details`, `get_closing_control_data`; `action_pos_session_validate` now takes `balancing_account` and `amount_to_balance`.

**pos.config**: `cash_control` is now computed (presence of a cash payment method); `default_cashbox_id` replaced by `default_bill_ids`; new `set_maximum_difference`; `last_session_closing_cashbox` removed.

**account.move**: new `pos_payment_ids`. **account.payment**: new `pos_payment_method_id`, `force_outstanding_account_id`, `pos_session_id`. **account.journal**: POS payment methods are now linked through `journal_id`. The invoice PDF uses `account.account_invoices` (`pos_invoice_report` removed). The POSS journal becomes a general journal, and the default POS receivable account is configurable in POS settings.

## How your habits should change

- Payment methods are now Cash / Bank / Customer Account, chosen through a journal: the separate "cash journal" field is gone, and the intermediary receivable account is optional (it falls back to company settings).
- "Advanced Cash Control" is no longer a setting: it is automatic as soon as a cash payment method exists.
- Coins/bills are configured as Coins/Bills records on the POS configuration, replacing the cashbox template.
- Counting cash at opening and closing happens in the POS. If closing fails (unbalanced entry, draft orders, difference above the allowed maximum without manager rights), you are redirected to the backend; rescue sessions must still be closed in the backend.
- Bank payments are no longer only statement lines: at closing they generate accounting payments booked on the payment method's outstanding account.

## What you gain by migrating

Cleaner POS accounting: invoices, payments and customer dues are reconciled automatically, so your accounting team stops fixing POS entries by hand and credit sales become possible. Cash handling is enforced and traceable, bank and cash payments reach the right journals and accounts, and closing is faster and better documented. End users also get a more comfortable POS: on-screen cash counting, packaging, pay later, ship later and faster sessions.

*Note: coupons, gift cards, margins, restaurant and other POS features listed in the 15.0 release notes belong to Enterprise editions and are not part of this Community addon.*
