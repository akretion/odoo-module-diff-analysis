# account migration guide (19.0 -> 20.0)

This guide covers the `account` addon differences between Odoo 19.0 and 20.0 (Community edition), for functional users and for teams planning a migration.

## What's new for users

- **Parent accounts replace account groups.** Groups (defined by code ranges) are gone. Your chart of accounts is now a real parent/child hierarchy: any account can have a parent, the hierarchy shows in accounting reports, and a parent can even be used as an intermediary account on an invoice line. Account codes become optional, and may now contain dashes (`-`) besides letters, digits and dots. The report option formerly called "Account Groups" is now "Account Parents".
- **Ledgers now include journals.** Journal groups no longer work by exclusion: you select which journals a ledger contains, each journal belonging to at most one ledger, with globally unique ledger names.
- **Total (cash) rounding is more capable.** Rounding methods have a sequence and can apply automatically based on conditions (currency, partner category, payment method). The method is selectable directly on the invoice for draft sales documents, and profit/loss accounts are defaulted automatically. The setting is renamed "Total Rounding".
- **Clearer bank data.** Account number, bank name and BIC are plainly named and shared between journals and partner bank accounts; IBAN and CLABE numbers are detected automatically. Phishing warnings flag risky accounts (money-transfer services, country mismatches).
- **Cleaner invoice line text.** The line name holds only the actual description; the product name is no longer copied into it. A new "Label" field combines product name and description, and EDI exports carry only the real description.
- **Everyday improvements:** payment statuses renamed ("In Process" → "Paid", "Paid" → "Reconciled"), exchange gains/losses consolidated into one expandable line per invoice, batch "Reset to Draft" from list views, manual reconciliation available on any account (checkbox renamed "Payment Reconciliation"), taxes restricted to their own fiscal position, payment wizard detecting already-pending payments, and zip download of invoice attachments.
- **New exchange rate providers** (Azerbaijan, Georgia, Kazakhstan, Saudi Arabia, Uzbekistan) and conversion rates displayed when registering foreign-currency payments.

## Technical data model changes

- **`account.group` removed**, replaced by parent accounts on `account.account`.
- **`account.account`**: `group_id` removed; added `parent_id`, `parent_ids`, `parent_path` (indexed), `code_path`, `name_path`; `_parent_store = True`; order now `code_path, account_type, name_path`; inactive parents are returned when they have active children; display name uses `name_path`; account codes may contain dashes.
- **`res.partner.bank`**: `acc_number` → `account_number`, `acc_holder_name` → `holder_name`, `acc_type` → `account_type` (+`iban`, `clabe`), `bank_id` (res.bank) → `bank_bic`; `has_iban_warning`, `has_money_transfer_warning`, `money_transfer_service`, `partner_country_name` removed, replaced by `has_phishing_warnings` / `phishing_warnings`; bank accounts are archived instead of deleted.
- **`account.journal`**: `bank_acc_number` → `bank_account_number`, `bank_id` → `bank_name`, new `bank_bic`; `journal_group_ids` (Many2many) → `journal_group_id` (Many2one). **`account.journal.group`**: `excluded_journal_ids` → `included_journal_ids` (One2many), `company_id` removed.
- **Binary → Json computed fields**: `needed_terms`, `invoice_payments_widget`, `invoice_outstanding_credits_debits_widget`, `tax_totals`, `payment_term_details`, `term_key`, `epd_key`, `epd_needed`, `discount_allocation_key`, `discount_allocation_needed`, `tag_ids_domain`, `account_templates`, `account_map`, `tax_map` (map keys become strings).
- **`account.tax.group`**: `tax_payable_account_id`, `tax_receivable_account_id`, `advance_tax_payment_account_id` removed (closing accounts now live on `account.return.type`).
- **`account.cash.rounding`**: added `sequence`, `currency_ids`, `partner_category_ids`, `payment_method_line_ids`; `company_id` no longer required; profit/loss accounts are stored, company-dependent computes; default precision 1.0.
- **`account.move` / `account.move.line`**: `translated_product_name` removed, `label` added, `name` is now "Description"; `invoice_cash_rounding_id` is computed/stored and `has_biggest_tax_cash_rounding_line` added.
- **`account.report.line`**: `foldable` (Boolean) → `foldability` (Selection).
