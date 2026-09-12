# account migration guide (19.0 -> 20.0)

## What's new for users

No official release-note extract matched this addon, so this summary is built from the code changes shipped in `account` for 20.0.

- **Accounts get a real hierarchy.** Account Groups (defined by code ranges) disappear. Accounts now have a *Parent Account*, so you can build a tree (e.g. *Automobile* → *Fuel*, *Insurance*) and use a parent as an intermediary account in entries. Reports display this hierarchy instead of code ranges, and dashboards no longer depend on prefix conventions.
- **Bank accounts, reworked.** Account holder, account number and BIC are entered directly, and the account type (IBAN / CLABE) is detected automatically. Risks are surfaced in one "phishing warning" block (country mismatch between holder, bank account and IBAN/CLABE, or use of a money-transfer service) instead of separate IBAN and money-transfer flags.
- **Ledgers (journal groups) become inclusive.** You choose which journals belong to a ledger instead of excluding journals, and a journal can be assigned a ledger directly.
- **Report line folding** is now an explicit choice: Always Unfolded / Never Unfolded / Foldable — useful when reports are regrouped dynamically.
- Bank statements gain explicit Draft / Post actions, plus integrity checks on hashed journals.
- New actions and widgets: download a move's documents as a ZIP, open reversal moves, reset a selection of moves to draft, delete duplicates, print a payment receipt, and accounting dashboard KPIs (cash flow, profitability, sale/purchase).

## Technical data model changes

- Removed model **`account.group`** (and its chart-template support).
- `account.account`: added `parent_id`, `parent_path`, `parent_ids`, `code_path`, `name_path` (`_parent_store`); removed `group_id`; new default ordering `code_path, account_type, name_path`; codes may now contain dashes; inactive parents are kept when they have active children.
- `account.journal`: `journal_group_ids` (M2M) → `journal_group_id` (M2o); `bank_acc_number`/`bank_id` → `bank_account_number`, `bank_name`, `bank_bic`; `unlink()` archives the bank account instead of deleting it.
- `account.journal.group`: `excluded_journal_ids` → `included_journal_ids` (One2many); `company_id` removed; name unique globally.
- `res.partner.bank`: `acc_number`→`account_number`, `bank_id`→`bank_bic`, `acc_holder_name`→`holder_name`, `acc_type`→`account_type`; `has_iban_warning`/`money_transfer_service` → `phishing_warnings` (Json) / `has_phishing_warnings`.
- Several computed container fields switch from **Binary to Json**: `needed_terms`, `tax_totals`, both payment widgets, `payment_term_details`, `term_key`, `epd_key`, `epd_needed`, discount-allocation keys/needed, `tag_ids_domain`, `account_templates`, `account_map`, `tax_map`. `needed_terms` is now a list of `(key, values)` pairs.
- `account.report.line.foldable` (Boolean) → `foldability` (Selection).
- `company_registry_placeholder` / `partner_company_registry_placeholder` are removed from `account`; the unified base field is used.
- Payment duplicate-reference matching now also considers `paid` documents.
- Tax helpers gain `document_tax_mode` / `product_uom` arguments, plus new rounding and down-payment methods.
- Signature delta: 31 methods modified, 288 added, 117 removed — review any custom extension of `account`.

## How your habits should change

- Stop managing *Account Groups* by prefixes: rebuild your groups as parent accounts and set *Parent Account* on each child. The report filter previously called "Account Groups" is now "Account Parents".
- Bank/vendor data entry: fill BIC, account number and holder; type detection and warnings are automatic. Trusting an account still requires the validation group.
- Journal configuration: assign one *Ledger* per journal; ledger membership is no longer company-scoped and no longer defined by exclusions.
- Report designers: renamed folding field, three values instead of a checkbox.

## What you gain by migrating

A discoverable, code-independent account hierarchy usable in entries and reports; safer payment data entry through consolidated phishing warnings; simpler ledger filtering; clearer, more reliable bank statement workflows; and a modernized, JSON-based data model that keeps integrations and custom modules aligned with Odoo's current APIs.
