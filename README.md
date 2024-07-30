# Dude, what did they do to my Odoo at version X?

[Odoo](https://odoo.com) is the best ERP in the world but it comes at the price of a crazy evolution pace!
The purpose of this repo is to bring this amount of change back under control using the
[odoo-module-diff](https://github.com/akretion/odoo-module-diff) Odoo commit analyser tool.

Browse the directory of the Odoo serie you plan to migrate to and find out what are the breaking changes
compared to the previous serie (so yes changes will stack up when migrating several versions).

Alternatively, you can also checkout the repo and use `find <serie>` to get an exhaustive list
of all the craziest changes to the new Odoo serie.

`odoo-module-diff` focuses on extracting data model breaking changes out of the crazy commit noise.
Of course, changes aren't just about the database structure, but these data model changes are the pain points
that will require migrations scripts (see [OpenUpgrade](https://github.com/OCA/OpenUpgrade) ) and
are certainly the place to start from.

So while scanning all commits between two Odoo series, `odoo-module-diff` will retain the very ones
removing or altering database tables and columns.

Eventually the biggest documented features where new tables or columns are added are also kept with the
`feat` prefix. But you usually hear about the new features from the Odoo marketing so the focus here
is really more with making migration pain points explicit.

Despite the fine tuning, `odoo-module-diff` might keep a few false positive commits or even miss a few
commits requiring trivial migrations. In fact, we optimized it so it tend to detect commits with trivial
migrations and skip them to avoid too much noise. For instance a commit simply removing a non relational
field will not be listed here. The [OpenUpgrade](https://github.com/OCA/OpenUpgrade) analysis tools or
even just the standard Odoo ORM will indeed likely deal well with such trivial changes.

Example: what are the main breaking changes for the account module in version 17? Out of the 978 commits 
in the account module between the release 16.0 and 17.0, `odoo-module-diff` will highlight only
these 6 breaking changes and 2 important features:

```text
ls -1 17.0/account
 c000_________+--_110016_ref-account-merge-repartition-lines-m2o-field.patch
 c001__-------###_110016_ref-account-remove-chart-template.patch
 c002_______---##_110274_imp-account-simplify-payment-term-usability.patch
 c003___________-_99209_imp-account-sale-repair-website-purchase-remove-setting-and-display-co.patch
 c006________---#_104223_ref-use-onboarding-module.patch
 c007__________--_130632_ref-account-make-alias-usage-standard.patch
 feat004__________111857_imp-account-send-print-with-documents.patch
 feat005__________117964_imp-account-dashboard-performances.patch
```

Explanations:

-  The 6 first files are CHANGE pseudo patches with the commit diff and commit explanations.
-  The 2 last files are FEATURE pseudo patches
-  The 1st number is just a sequence to reflect the order the commits were made
-  The 2nd number is the Github PR number (you'll also find it in the 1st line of the pseudo patch alongh with the commut sha code)
-  The +|=|# symbols are a kind of heat representation of the changes (like git). + are for field additions, - for for deletions and # is for the size of the diff in general.
