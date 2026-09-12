# Dude, what did they do to my Odoo at version 17.0?

You can see below the Odoo addons that got the largest data model
changes between versions 16.0 and 17.0:
(this is just summing the size of the data model impacting commits
addon per addon; method signature deltas are in each addon's
method_signatures.patch)
You can browse each directory to dig into the detail of these changes.

1. [project](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/project) - 604K (10 commits)
2. [account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/account) - 384K (8 commits)
3. [hr_expense](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/hr_expense) - 246K (6 commits)
4. [stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/stock) - 202K (4 commits)
5. [mail](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/mail) - 164K (12 commits)
6. [hr_holidays](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/hr_holidays) - 136K (3 commits)
7. [repair](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/repair) - 107K (2 commits)
8. [account_edi_ubl_cii](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/account_edi_ubl_cii) - 95K (3 commits)
9. [delivery](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/delivery) - 71K (2 commits)
10. [payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/payment) - 55K (5 commits)
11. [mrp](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/mrp) - 51K (2 commits)
12. [base_automation](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/base_automation) - 48K (1 commit)
13. [pos_self_order](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/pos_self_order) - 43K (3 commits)
14. [base](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/base) - 39K (3 commits)
15. [hr_timesheet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/hr_timesheet) - 37K (2 commits)
16. [hr_attendance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/hr_attendance) - 34K (1 commit)
17. [hr](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/hr) - 32K (2 commits)
18. [mass_mailing](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/mass_mailing) - 32K (4 commits)
19. [payment_stripe](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/payment_stripe) - 31K (1 commit)
20. [product](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/product) - 31K (1 commit)
21. [pos_online_payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/pos_online_payment) - 30K (1 commit)
22. [analytic](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/analytic) - 29K (2 commits)
23. [im_livechat](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/im_livechat) - 29K (1 commit)
24. [sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/sale) - 28K (3 commits)
25. [hr_homeworking](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/hr_homeworking) - 28K (1 commit)
26. [survey](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/survey) - 27K (1 commit)
27. [pos_restaurant](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/pos_restaurant) - 27K (2 commits)
28. [maintenance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/maintenance) - 26K (1 commit)
29. [sms](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/sms) - 24K (1 commit)
30. [point_of_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/point_of_sale) - 24K (2 commits)
31. [event](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/event) - 22K (2 commits)
32. [mrp_account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/mrp_account) - 21K (1 commit)
33. [onboarding](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/onboarding) - 20K (1 commit)
34. [account_peppol](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/account_peppol) - 18K (3 commits)
35. [rating](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/rating) - 17K (1 commit)
36. [event_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/event_sale) - 16K (2 commits)
37. [mrp_subcontracting](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/mrp_subcontracting) - 16K (1 commit)
38. [gamification](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/gamification) - 16K (1 commit)
39. [payment_authorize](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/payment_authorize) - 15K (2 commits)
40. [account_edi](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/account_edi) - 13K (2 commits)
41. [purchase](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/purchase) - 13K (1 commit)
42. [base_vat](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/base_vat) - 12K (1 commit)
43. [purchase_stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/purchase_stock) - 12K (2 commits)
44. [stock_picking_batch](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/stock_picking_batch) - 11K (1 commit)
45. [sale_project](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/sale_project) - 9.5K (2 commits)
46. [account_edi_proxy_client](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/account_edi_proxy_client) - 9.2K (1 commit)
47. [account_tax_python](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/account_tax_python) - 8.0K (1 commit)
48. [payment_adyen](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/payment_adyen) - 7.5K (1 commit)
49. [pos_hr](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/pos_hr) - 7.2K (1 commit)
50. [payment_paypal](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/payment_paypal) - 5.9K (1 commit)
51. [sale_timesheet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/sale_timesheet) - 5.9K (1 commit)
52. [sale_loyalty](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/sale_loyalty) - 5.4K (1 commit)
53. [base_import](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/base_import) - 5.3K (1 commit)
54. [hr_org_chart](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/hr_org_chart) - 5.1K (1 commit)
55. [hr_recruitment_survey](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/hr_recruitment_survey) - 4.7K (1 commit)
56. [payment_asiapay](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/payment_asiapay) - 4.5K (1 commit)
57. [hr_fleet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/hr_fleet) - 4.5K (1 commit)
58. [event_booth_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/event_booth_sale) - 4.1K (1 commit)
59. [account_payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/account_payment) - 4.1K (1 commit)
60. [web](https://github.com/akretion/odoo-module-diff-analysis/blob/main/17.0/web) - 2.4K (1 commit)

In total: 61 addons, 129 data model
impacting commits, 3.0M of pseudo patches.

Generated by [odoo-module-diff](https://github.com/akretion/odoo-module-diff): these numbers are
heuristic (see the repo README); the full pseudo patches are in each
addon directory.
