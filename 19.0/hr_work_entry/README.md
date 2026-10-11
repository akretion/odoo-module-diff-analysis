# hr_work_entry migration guide (18.0 -> 19.0)

This addon generates the work entries (hours) used by HR and payroll. Odoo 19.0 rebuilds it on the new employee versioning model and changes how a work entry is stored.

## What's new for users

- **Employees and contracts merged.** Contracts are no longer a separate object: they become *versions* of the employee record, and work entries are generated for a version.
- **Work entries are a date + a duration.** No more "From/To" datetimes: one day and a number of hours.
- **Easier manual entry.** The Work entries widget has been reworked to make manual creation simpler.
- **Choose the generation source.** Each version states where its work entries come from — working schedule by default, attendance/planning sources when those apps are installed.
- A regeneration wizard recomputes entries when a schedule changes.

Pay runs, payslip reports, salary attachments and benefits belong to the Payroll application and are out of scope here.

## Technical data model changes

**Models**
- `hr.contract` is gone; contract data lives in the new `hr.version` model, inherited on `hr.employee`. `hr.work.entry.version_id` now points to `hr.version`.
- New model `hr.user.work.entry.employee` (personal work-entry calendar filter), unique per (user_id, employee_id).
- Contract lookups go through `_get_versions_with_contract_overlap_with_period`.

**hr.version**
- `date_start`/`date_end` replaced by mandatory `date_version` and `contract_date_start`/`contract_date_end`; `date_start`/`date_end` are computed, non-stored.
- Added `date_generated_from`, `date_generated_to`, `last_generation_date`, `work_entry_source`, `work_entry_source_calendar_invalid`.

**hr.work.entry**
- Removed `date_start`, `date_stop` and the computed `duration`; ordering is now `conflict desc, state, date`.
- Added `date` (Date, required) and `duration` (Float, default 8).
- Removed the "must end", "start before end" and "validated work entries cannot overlap" constraints, plus the (date_start, date_stop) index. New index: (version_id, date).
- Conflicts are now flagged per employee and day when the total duration exceeds 24 hours, instead of overlapping datetime ranges.

**Methods**
- `generate_work_entries(date_start, date_stop, force=False)` now lives on `hr.employee` (delegating to the versions overlapping the period) and on `hr.version`.
- Helpers: `_get_work_entries_values`, `_remove_work_entries`, `_cancel_work_entries`, `_recompute_work_entries(date_from, date_to)`, `_generate_work_entries_postprocess`.
- `_set_current_contract` now takes a single `date`.
- Cron `_cron_generate_missing_work_entries` works in batches of 100 versions.

Generated values are post-processed: entries spanning several local days are split per day, converted to date + duration, then merged per day and type. A timezone is required on the calendar, employee or company.

## How your habits should change

- Stop looking for a Contract form: manage versions directly on the employee; work entries always belong to a version.
- Enter a date and hours instead of from/to times. Overlaps are no longer errors — instead watch the 24-hour daily total.
- Changing a working schedule or the work entry source recomputes already generated entries through the regeneration wizard, and updates the last generation date.
- Keep timezones filled in, or generation fails.
- Reports and SQL must use `date` and `duration`, and join `hr.version` for contract data.

## What you gain by migrating

- One source of truth: employee versioning removes contract/employee duplication and mismatched work entries.
- Simpler, less error-prone input: one merged line per day and per work entry type.
- Robust conflict detection on real daily totals, plus correct timezone and flexible-schedule handling.
- Automatic regeneration on schedule changes, batched cron generation and leaner indexes on large volumes.
