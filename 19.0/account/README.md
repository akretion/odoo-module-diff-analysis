# account migration guide (18.0 -> 19.0)

Applies to the Community `account` addon. Enterprise-only items from the 19.0 Accounting release notes (full bank reconciliation widget, accounting/follow-up reports, OCR digitisation, tax returns, asset templates) are not covered here.

## What's new for users

**Receipts become first-class documents.** Purchase receipts are always available: switch between Bill and Receipt directly on the vendor bill form. Sales receipts are enabled once in Settings (no security group needed), get list-view filters, and companies can define default sale/purchase receipt taxes applied before product or account taxes. The "Professional" percentage (partially deductible bills) now also works on purchase receipts.

**Better account selection.** Accounts can carry a description explaining when to use them. On invoices income accounts are proposed first; on bills, expenses and fixed assets. Default taxes on accounts now apply only to invoices and bills, not to miscellaneous entries.

**Fiscal positions reworked.** Tax mappings are removed: a tax declares which fiscal positions it applies to and which taxes it replaces (e.g. a 0% export tax replaces domestic sale taxes). Fiscal positions are always chosen by sequence with applicability filters; replaced taxes are hidden by default and remain reachable via "Search More".

**Tax reports.** The + / - signs have been removed from tax tags; inversions are now handled on report lines.

**Blocking warnings removed.** Partner "Invoice" warning fields and their setting are gone; warnings from Purchase/Sale are informational only and no longer block or reset the partner.

**Also improved:** light audit trail enabled by default; duplicate-bill warnings remain visible after posting; resetting an invoice to draft detaches the generated file; deferred miscellaneous entries get start/end dates; a taxable supply date is activated where legally required; clearer payment communication formats; improved default recipient bank account on invoices; cleaned-up reconciliation model forms and dashboard links; renamed menu actions for readable URLs.

## Technical data model changes

Removed: `res.partner.invoice_warn` / `invoice_warn_msg`; `res.config.settings.group_warning_account`; `group_show_sale_receipts` / `group_show_purchase_receipts` (replaced by the `account.show_sale_receipts` config parameter); the three `module_account_bank_statement_import_*` settings toggles (OFX/CSV/CAMT are no longer shown).

Added: `res.company.account_sale_receipt_tax_id` and `account_purchase_receipt_tax_id`; `account.move.is_receipt()`; the `kpi.provider` model (`get_account_kpi_summary`); account placeholder-code search.

Changed behaviour:
- `account.move.line.parent_id` is now computed, not stored; section logic moved to `_get_section_lines` / `get_parent_section_line`.
- Tax tags: the invert mechanism is removed (`_compute_tax_tag_invert` deleted, `_translate_tax_tags` added), matching the report change.
- Writing on `account.code` now creates `code.mapping` records (`account.code.mapping` compute/create overrides).
- Audit trail: `mail.message` flags renamed (`account_audit_log_activated` → `account_audit_log_restricted`, plus a preview flag); new company checks for restrictive audit trails.
- Analytic lines are kept synchronised with journal items (new create/write/unlink overrides, `_update_analytic_distribution`).

Method signatures: 40 modified, 284 added, 81 removed. Integrators should note `default_get(fields)`, `write(vals)`, `name_search(..., domain=...)`, `read_group()` replaced by `formatted_read_group()`, `_field_to_sql(alias, field_expr, query=None)`, `_get_default_amls_matching_domain(allow_draft=False)`, `chart.template.try_loading(..., force_create=True)`, `_get_invoice_report_filename(extension, report=None)`, `is_sale_document/is_purchase_document(..., move_type=False)`, `_autopost_draft_entries(batch_size)`, and many new tax-computation helpers (`_prepare_base_line_for_taxes_computation`, `_add_tax_details_in_base_lines`, `_round_base_lines_tax_details`).

## How your habits should change

- Ignore partner invoice warnings; messages from Purchase/Sale are informative only.
- Configure tax applicability and replacement on taxes, and ordering on fiscal positions, instead of tax mappings.
- Enable sale receipts once in Settings; purchase receipts need no setting.
- Read tax reports without +/- tag signs; use "Search More" to add back a replaced tax.

## What you gain by migrating

Cleaner accounting screens, flexible receipts, fiscal positions and tax reports that are easier to justify to auditors, an audit trail active without configuration, and a modernised ORM API that keeps your customisations maintainable.
