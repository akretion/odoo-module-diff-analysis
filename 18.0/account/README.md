# account migration guide (17.0 -> 18.0)

Odoo 18.0 reshapes the Invoicing core: payments, lock dates, shared accounts and Send & Print. Here is what changes for users and for your data.

## What's new for users

- **Payments without accounting entries**: a payment only creates a journal entry if an outstanding account is set on its payment method. Recording a payment is now mainly about avoiding duplicates and preparing bank files.
- **New payment states**: Draft, In Process (previously Posted), Paid, Canceled, Rejected.
- **Block Payment** action on invoices prevents registering a payment.
- **Register payments on draft invoices**; installment amounts from payment terms are proposed.
- **Preferred payment method per partner** (incoming/outgoing), used by default and usable as a filter.
- **Preferred invoicing method and e-invoice format per contact**, driving a simplified Send & Print: one channel by default instead of ticking everything each time.
- **Lock dates**: sales and purchases now lock separately, plus a Hard Lock Date. (The lock-date wizard itself is Enterprise.)
- **Shared accounts between companies**: one chart of accounts for several companies, with merge/de-merge tools.
- **Credit card journals**, check layout selection, QR codes in the bills payment wizard, editable reconciliation amount.
- **Invoicing dashboard/onboarding** refreshed for Invoicing-only users.
- Also: duplicate bill detection, autopost bills, abnormal invoice alerts, journal email aliases, invoice line catalogue view, exchange rate stored on invoices.

## Technical data model changes

- account.payment no longer inherits account.move: move_id is optional; new name, date, state, memo, is_sent fields; journal_id/company_id are computed; destination_journal_id and is_internal_transfer removed.
- New many2many invoice_ids / matched_payment_ids link payments and invoices without reconciliation.
- account.move.payment_id renamed origin_payment_id; new matched_payment_ids, payment_count and the 'blocked' payment_state.
- Company-level outstanding accounts (account_journal_payment_debit/credit_account_id) and all fallbacks to them were removed.
- account.account: company_id replaced by company_ids; code is company-dependent (one code per company); group_id/root_id are no longer stored.
- res.company: period_lock_date removed, replaced by sale_lock_date and purchase_lock_date; new hard_lock_date and user_* fields; max_tax_lock_date removed. New model account.lock_exception; the account_lock module is gone.
- res.partner: has_unreconciled_entries, last_time_entries_checked and mark_as_reconciled() removed; receivable/payable account fields now use ondelete='restrict'.
- account.journal: sale_activity_type_id / user_id / note removed.
- account.move: send_and_print_values renamed sending_data; Send & Print split into account.move.send.wizard (single) and account.move.send.batch.wizard (batch).
- Methods renamed: _check_fiscalyear_lock_date → _check_fiscal_lock_dates; _get_accounting_date takes a lock_dates argument.
- Core: company-dependent fields (e.g. account code) are now stored as jsonb, so they can be searched and reported on.

## How your habits should change

- Do not expect a journal entry for every payment. Set an outstanding account on the payment method line only if you want one.
- Bank reconciliation becomes the reference: match bank transactions to invoices, and handle transfers between your own journals as a write-off instead of an Internal Transfer payment.
- Use Canceled / Rejected instead of deleting payments that never reached the bank.
- Payments without entries no longer count in the invoice paid amount; they show as In Process.
- Create accounts with the company context, then set a code for each company before adding more companies.
- Review lock dates: split sales/purchase locks replace the old period lock date, and the Hard Lock Date cannot be undone.
- In Send & Print, set each partner's preferred channel once instead of choosing options at every send.

## What you gain by migrating

- Cleaner receivable/payable balances and easier bank reconciliation, with fewer duplicate payments.
- One shared chart of accounts for multi-company setups.
- Finer period control through per-journal locks and a defensible hard lock.
- Faster reconciliation (batched queries) and better structured company-dependent data (jsonb).
- A Send & Print flow that scales to batch sending and e-invoicing.
