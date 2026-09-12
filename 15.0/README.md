# Dude, what did they do to my Odoo at version 15.0?

You can see below the Odoo addons that got the largest data model
changes between versions 14.0 and 15.0:
(this is just summing the size of the data model impacting commits
addon per addon; method signature deltas are in each addon's
method_signatures.patch)
You can browse each directory to dig into the detail of these changes.

1. [account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/account) - 172K (5 commits)
2. [payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/payment) - 169K (2 commits)
3. [mail](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/mail) - 137K (7 commits)
4. [sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/sale) - 117K (2 commits)
5. [hr_holidays](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/hr_holidays) - 110K (1 commit)
6. [stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/stock) - 100K (3 commits)
7. [point_of_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/point_of_sale) - 87K (2 commits)
8. [project](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/project) - 77K (5 commits)
9. [mail_group](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/mail_group) - 72K (2 commits)
10. [crm](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/crm) - 62K (4 commits)
11. [mass_mailing](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/mass_mailing) - 61K (3 commits)
12. [payment_authorize](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/payment_authorize) - 57K (1 commit)
13. [payment_stripe](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/payment_stripe) - 45K (1 commit)
14. [event](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/event) - 44K (5 commits)
15. [sale_timesheet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/sale_timesheet) - 38K (3 commits)
16. [base](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/base) - 36K (3 commits)
17. [sales_team](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/sales_team) - 34K (3 commits)
18. [fleet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/fleet) - 31K (2 commits)
19. [payment_adyen](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/payment_adyen) - 27K (1 commit)
20. [website](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/website) - 26K (2 commits)
21. [hr](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/hr) - 24K (3 commits)
22. [payment_sips](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/payment_sips) - 23K (1 commit)
23. [payment_alipay](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/payment_alipay) - 21K (1 commit)
24. [payment_paypal](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/payment_paypal) - 21K (1 commit)
25. [payment_buckaroo](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/payment_buckaroo) - 18K (1 commit)
26. [payment_payulatam](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/payment_payulatam) - 17K (1 commit)
27. [lunch](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/lunch) - 17K (2 commits)
28. [google_calendar](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/google_calendar) - 16K (1 commit)
29. [hr_fleet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/hr_fleet) - 14K (1 commit)
30. [payment_payumoney](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/payment_payumoney) - 14K (1 commit)
31. [pos_adyen](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/pos_adyen) - 13K (1 commit)
32. [payment_transfer](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/payment_transfer) - 12K (1 commit)
33. [product](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/product) - 12K (1 commit)
34. [gift_card](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/gift_card) - 8.9K (1 commit)
35. [auth_totp](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/auth_totp) - 8.3K (1 commit)
36. [calendar](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/calendar) - 7.9K (1 commit)
37. [project_timesheet_holidays](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/project_timesheet_holidays) - 7.7K (1 commit)
38. [mrp](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/mrp) - 6.9K (1 commit)
39. [event_sms](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/event_sms) - 6.1K (1 commit)
40. [sale_project](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/sale_project) - 6.0K (1 commit)
41. [stock_account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/stock_account) - 5.6K (1 commit)
42. [delivery](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/delivery) - 5.6K (1 commit)
43. [hr_expense](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/hr_expense) - 5.0K (1 commit)
44. [pad](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/pad) - 4.3K (1 commit)
45. [phone_validation](https://github.com/akretion/odoo-module-diff-analysis/blob/main/15.0/phone_validation) - 3.9K (1 commit)

In total: 45 addons, 85 data model
impacting commits, 1.8M of pseudo patches.

Generated by [odoo-module-diff](https://github.com/akretion/odoo-module-diff): these numbers are
heuristic (see the repo README); the full pseudo patches are in each
addon directory.
