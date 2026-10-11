# hr_holidays_attendance migration guide (19.0 -> 20.0)

## What's new for users

The official release notes contain **no dedicated entry** for `hr_holidays_attendance`. The Attendances and Time Off sections list features that belong to other apps (and several are Enterprise-only), so this guide is based on the addon's own changes.

* Overtime compensation is now driven by **rules**, not checkboxes. A rule states: "when an employee works more (or less) than X hours on a day/week, reclassify the excess (or deficit) and grant (or deduct) N% of those hours as compensatory time off."
* Rules run **in sequence**: each rule sees the already-reclassified output of the previous ones, so a second threshold can build on the first.
* Compensatory leave is now a real **time off allocation**, instead of a special "extra hours" leave type deducted on approval.
* Every credit is **logged per source attendance**, and automatically reversed when the attendance's check-in/check-out/employee changes, when it is refused, or when it is deleted.
* Leaves produced by a rule show their duration in the record name.
* "Overtime hours last month" on the employee is computed from attendances classified by a rule.
* Only **validated** attendances count: for worked-hours accrual plans and for work entry generation.

## Technical data model changes

Removed:
* `hr.work.entry.type.overtime_deductible` (and its display-name and allocation-data overrides).
* `hr.attendance.overtime.line`: `compensable_as_leave`, `leave_compensation_rate`.
* `hr.attendance.overtime.rule`: `compensable_as_leave`, `leave_compensation_rate`, `_extra_overtime_vals`.
* `hr.leave`: `employee_overtime`, `overtime_deductible`, `_check_overtime_deductible`, `_update_leaves_overtime`.
* `hr.leave.allocation`: `overtime_deductible`, `employee_overtime`, `_check_employee_overtime_balance`, `deduct_extra_hours` default.
* `hr.employee._get_deductible_employee_overtime`, and the `hr.attendance` SQL index creation.

Added / changed:
* New `hr.time.rule` engine (from `hr_work_entry`) and `hr.time.rule.allocation.log`; this addon plugs in `_apply_attendance_output` so attendance excess/deficit also creates or deducts allocations.
* New `res.company.attendance_work_entry_type_id` (working-time type, no allocation).
* `hr.attendance.write()` now reverses credits when time/employee fields change or the state becomes refused/draft; deletion reverses too.
* `hr.employee.get_attendance_data_by_employee()` (renamed from `get_attendace_data_by_employee`) now returns `overtime_hours` instead of `unspent_compensable_overtime`; `_compute_hours_last_month` feeds `hours_last_month_overtime`.
* `hr.leave._compute_display_name()` adds the duration for rule-generated leaves.
* `hr.version` builds work entries from validated attendances; overlaps between attendance and working-time leave are arbitrated by `work_entry_type.sequence`.

## How your habits should change

* Rebuild your overtime configuration as `hr.time.rule` records with an explicit sequence; the "Deduct Extra Hours" flag on time off types and the "Give back as time off" option on overtime rules are gone.
* Employees now request compensatory time off against a standard allocation.
* The "not enough extra hours" errors at request and approval disappear; deficits are filtered against the available allocation.
* Timing: daily excess rules trigger on create/write; daily undertime is processed the next day by cron; weekly undertime only runs via the weekly cron, so past corrections do not retroactively trigger weekly undertime.
* Editing or deleting an attendance now reverses the credits it produced.
* Check accrual plans based on worked hours: draft attendances no longer count.

## What you gain by migrating

* A single, sequenced rule engine that composes cleanly, instead of one flag per work entry type.
* Full traceability: each allocation credit is logged per source record and precisely reversible.
* Compensatory hours become normal time off balances, visible in standard Time Off reporting.
* A leaner data model built on validated attendances only.
