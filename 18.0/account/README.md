# account migration guide (17.0 -> 18.0)

## What's new for users

- **Payments without accounting entries.** A payment only creates a journal entry if an outstanding account is set on its payment method line. Payments now have their own states: Draft, In Process, Paid, Canceled, Rejected.
- **Block Payment on invoices**, preventing payment registration; blocked invoices show a dedicated payment status.
- **Register payments on draft invoices and bills**; installments from the payment terms are pre-filled.
- **Lock dates reworked.** The Journal Entries Lock Date becomes a Sales Lock Date and a Purchase Lock Date, plus an irreversible Hard Lock Date. Per-user lock date exceptions are part of `account` (the wizard to create them ships with Enterprise).
- **Shared accounts between companies:** one chart of accounts for several companies, with a code per company and merge/de-merge tools.
- **Preferred invoicing method per partner** and e-invoice format, used by Send & Print, which was redesigned (one preferred channel per partner, easy batch sending).
- **New company setting** defining sales taxes as price-included or price-excluded.
- Also: duplicate invoice/bill detection, auto-post bills from selected vendors, email aliases on miscellaneous journals, bank statement import even when lines are not sorted by date, and the "Days end of month on the" payment term option.

## Technical data model changes

- `account.payment` no longer inherits `account.move`: `move_id` is optional. New fields `name`, `date`, `state`, `memo`, `company_currency_id`, `invoice_ids`, `attachment_ids`; removed `is_internal_transfer`, `destination_journal_id`, `duplicate_move_ids` (now `duplicate_payment_ids`).
- `account.move.payment_id` became `origin_payment_id`; new `matched_payment_ids` and `payment_count`; `account.move.line.payment_id` follows it.
- New invoice payment status `blocked` and action `action_toggle_block_payment`.
- Company-level outstanding accounts were removed on `res.company`: outstanding accounts are now set only on payment method lines.
- `account.account`: `company_id` replaced by `company_ids`; `code` is company-dependent; `group_id` and `root_id` are no longer stored; account merge/de-merge added.
- Lock dates: `period_lock_date` and `max_tax_lock_date` removed; new `sale_lock_date`, `purchase_lock_date`, `hard_lock_date`, computed `user_*_lock_date` fields and model `account.lock_exception`. Module `account_lock` is removed.
- `account.move.send_and_print_values` renamed to `sending_data`; new sending models. `invoice_is_email` / `invoice_is_download` removed in favour of partner sending preferences.
- Tax computation unified: `tax_key`, `compute_all_tax`, `total_tax_factor` removed from move lines; `tax_totals` payload restructured.
- Removed: partner `has_unreconciled_entries`, `last_time_entries_checked` and `mark_as_reconciled()`; sale journal activity fields; `is_downpayment` on move lines. Company-dependent fields (partner/product accounts...) are now stored as JSONB with `ondelete='restrict'`.
- Renamed methods: `_check_fiscalyear_lock_date` → `_check_fiscal_lock_dates`, `_search_new_account_code(start_code, cache=None)`, `_get_accounting_date(..., lock_dates=None)`.

## How your habits should change

- Recording a payment is now an extra-accounting step: the bank transaction is what hits the accounts. Match bank transactions to invoices instead of relying on payment entries; internal transfers are replaced by a write-off during bank reconciliation.
- Payments without a journal entry no longer count in the paid amount of an invoice; follow them through the payment status and the new Payments button.
- Instead of a single period lock date, use sales, purchase or global lock dates, and grant a temporary exception rather than raising user rights. The hard lock date cannot be undone.
- In multi-company, keep one shared chart of accounts and set a code for every company the account belongs to.
- In Send & Print, rely on the partner's preferred channel instead of ticking options on every invoice.
- After migration, check a few invoices: with round globally and price-included taxes (or analytic distributions), tax totals can differ by a cent from 17.0.

## What you gain by migrating

- Cleaner customer and vendor balances: payment entries no longer clutter the ledger and the bank date becomes the accounting reference.
- Simpler, safer bank reconciliation with fewer duplicate or mismatched payments.
- One chart of accounts shared by all your companies, with per-company codes and merge tools.
- Finer closing control: separate sales/purchase locking, an irreversible hard lock, and auditable exceptions.
- Faster batched Send & Print with per-partner sending preferences.
