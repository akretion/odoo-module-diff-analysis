# account migration guide (17.0 -> 18.0)

This guide summarizes what changes in the `account` addon between Odoo 17.0 and 18.0, for functional users. It describes the Community edition.

## What's new for users

- **Reworked lock dates.** Locking is now done by journal type (sales, purchases, taxes), with a new hard lock date and an exceptions mechanism (grant a temporary unlock to a user, with an audit trail). A warning explains what is blocked and why.
- **Shared accounts between companies.** The same account can now belong to several companies of your database; the code is computed per company, and accounts can be merged and un-merged.
- **Payments without accounting entries.** A payment no longer creates a journal entry unless the payment method has an outstanding account defined. Payments now have their own state and validate/reject actions.
- **Register payments on draft invoices and bills**, and pay in installments based on the payment terms; the portal shows what is already paid.
- **Preferred payment method per partner** (incoming and outgoing), used by default when creating payments.
- **Better duplicate detection on vendor bills**: draft and posted documents are searched, with smart links to the potential duplicates.
- **New credit card journal type** to record credit card payments, upload statements and reconcile them.
- **Reconciliation models can generate a customer invoice or vendor bill** directly from a bank transaction.
- **Sales taxes price included/excluded** are driven by a company setting, and can be overridden on individual taxes.
- **Legal notes on taxes**, displayed on documents when the tax is used.
- **Analytic distribution models** can be sequenced and combined across plans.
- **Email aliases on miscellaneous journals**: incoming emails with attachments create journal entries automatically.
- **Exchange rate is stored on invoices** and displayed.
- **Annual/fiscal-year journal sequences** support staggered fiscal years; matching numbers are simplified and color-coded.
- **Audit trail improvements** (GoBD-oriented): changes on journal items are tracked.

## Technical data model changes

Removed fields and methods:
- `account.journal`: `sale_activity_type_id`, `sale_activity_user_id`, `sale_activity_note` (customer payments follow-up on sale journals was removed).
- `res.partner`: `has_unreconciled_entries`, `last_time_entries_checked` and `mark_as_reconciled()` were removed (unused).

Notable changes:
- `account.account`: company-shared account codes (`_compute_code`, `_ensure_code_is_unique`, `_search_code`), new account unmerge tool (`action_unmerge`), `_search_new_account_code(start_code, cache=None)`, `_constrains_code` removed.
- `account.lock_exception`: reworked with one lock date per exception, new state, revoke action and audit-trail view.
- `account.payment`: new state/validation flow (`action_validate`, `action_reject`, `_generate_journal_entry`); the move synchronisation methods were removed.
- `account.move.send`: large new model supporting the Send & Print flow.
- `account.tax`, `account.move`, `account.move.line`: tax computation API reworked (`_compute_taxes(base_lines, company, …)`, `compute_all(..., rounding_method=…)`), `_get_accounting_date(..., lock_dates=None)`, `account.payment.term._compute_terms(..., cash_rounding=None)`.
- `account.bank.statement`, `account.bank.statement.line`, `account.payment`: new `init()` (denormalised journal/company).

## How your habits should change

- Use the new lock-date screen: select journal types, set a hard lock, and grant exceptions instead of a single fiscal-year lock.
- Do not expect a journal entry for every payment: it depends on the outstanding account set on the payment method.
- Configure price-included/excluded once at company level rather than tax by tax.
- Accounts are no longer strictly company-specific: check sharing rules when creating or merging accounts.
- Old sale-journal "payment follow-up activity" settings are gone; schedule activities manually if needed.

## What you gain by migrating

- Stronger, more granular period locking and a real audit trail (GoBD).
- Simpler multi-company accounting: shared accounts, no duplicated charts, merge/unmerge tools.
- A cleaner payment flow: draft payments, installments, preferred payment methods, fewer useless entries.
- Less manual work on bank reconciliation and vendor-bill duplicates.
- A maintained release: 17.0 is reaching end of life, 18.0 keeps you on a supported, updated version.
