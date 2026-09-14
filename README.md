# Dude, what did they do to my Odoo at version X?

[Odoo](https://odoo.com) is the best ERP in the world but it comes at the price of a crazy evolution pace!
The purpose of this repo is to bring this amount of change back under control using the
[odoo-module-diff](https://github.com/akretion/odoo-module-diff) Odoo commit analyzer tool.

Browse the directory of the Odoo serie you plan to migrate to and find out what are the breaking changes
compared to the previous serie (so yes, changes stack up when you migrate several versions: use the
per-serie `README.md` of each intermediate serie for the full picture).

Each serie directory contains:

- a `README.md` ranking the most impacted addons of the serie ("Dude, what did
  they do to my `account` module?"), linking to each addon directory;
- one directory per impacted addon with its pseudo patches (see the file naming
  explanation below) and, for the 30 most impacted addons, an AI-generated
  `README.md` migration guide written for functional users: what changed for
  them, how their habits should change and what they gain by migrating;
- a `RELEASE_NOTE.md` conversion of the official Odoo release notes for the
  serie (series 13 to 19; series before 13 have no official release notes page
  anymore), used together with the pseudo patches to write the addon READMEs.

Alternatively, you can also check out the repo and use `find <serie>` to get an exhaustive list
of all the most significant changes of the new Odoo serie.

`odoo-module-diff` focuses on extracting data model breaking changes out of the crazy commit noise.
Of course, changes aren't just about the database structure, but these data model changes are the pain points
that will require migration scripts (see [OpenUpgrade](https://github.com/OCA/OpenUpgrade)) and
are certainly the place to start from.

So while scanning all commits between two Odoo series, `odoo-module-diff` will retain the very ones
removing or altering database tables and columns.

Eventually the biggest documented features where new tables or columns are added are also kept with the
`feat` prefix. But you usually hear about the new features from the Odoo marketing so the focus here
is really more with making migration pain points explicit.

Despite the fine tuning, `odoo-module-diff` might keep a few false positive commits or even miss a few
commits requiring trivial migrations. In fact, we optimized it so it tends to detect commits with trivial
migrations and skip them to avoid too much noise. For instance a commit simply removing a non relational
field will not be listed here. The [OpenUpgrade](https://github.com/OCA/OpenUpgrade) analysis tools or
even just the standard Odoo ORM will indeed likely deal well with such trivial changes.

Example: what are the main breaking changes for the account module in version 17? Out of the 978 commits 
in the account module between the release 16.0 and 17.0, `odoo-module-diff` will highlight only
these 6 breaking changes and 2 important features:

```text
ls -1 17.0/account
 c000_________+--_110016_ref-account-merge-repartition-lines-m2o-field.patch
 c001__-------###_110016_ref-account-remove-chart-template.patch
 c002_______---##_110274_imp-account-simplify-payment-term-usability.patch
 c003___________-_99209_imp-account-sale-repair-website-purchase-remove-setting-and-display-co.patch
 c006________---#_104223_ref-use-onboarding-module.patch
 c007__________--_130632_ref-account-make-alias-usage-standard.patch
 feat004__________111857_imp-account-send-print-with-documents.patch
 feat005__________117964_imp-account-dashboard-performances.patch
```

Explanations:

-  The 6 first files are CHANGE pseudo patches with the commit diff and commit explanations.
-  The 2 last files are FEATURE pseudo patches.
-  The 1st number is just a sequence reflecting the order in which the commits were made.
-  The 2nd number is the GitHub PR number (you'll also find it in the 1st line of the pseudo patch
   along with the commit sha).
-  The `+`, `-` and `#` symbols are a kind of heat representation of the changes (like git):
   `+` stands for field additions, `-` for deletions and `#` for the size of the diff in general.

## Using this repo with the odoo-module-diff tool

This repo is meant to be cloned locally and consumed by the
[odoo-module-diff](https://github.com/akretion/odoo-module-diff) tool: point it here with
`export ODOO_MODULE_DIFF_HOME=/path/to/this/clone` and use its `--dump-context` option to
aggregate the pseudo patches of an addon (and its dependency chain) into a single
AI-agent-friendly migration context. See the
[odoo-module-diff README](https://github.com/akretion/odoo-module-diff#usage-dump-a-migration-context-the-default-use-case)
for the details.

## Caveats

- The pseudo patches show the **intent** of each breaking change. A pseudo commit is often
  followed by many smaller fix commits: once you detected a potential migration issue, always
  check the real Odoo code base of the target serie as the authoritative reference.
- Non core addons (OCA, custom project addons) are not analysed in this repo: the
  odoo-module-diff tool analyses them on the fly from your local clones (see its README).
- Series before 13.0 are no longer maintained here (no per addon README summaries, no
  release notes conversion); they are kept for the older pseudo patches only.
