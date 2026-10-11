# Dude, what did they do to my Odoo at version 20.0?

You can see below the Odoo addons that got the largest data model
changes between versions 19.0 and 20.0:
(this is just summing the size of the data model impacting commits
addon per addon; method signature deltas are in each addon's
method_signatures.patch)
You can browse each directory to dig into the detail of these changes.

1. [point_of_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/point_of_sale) - 547K (15 commits)
2. [hr_holidays](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_holidays) - 355K (10 commits)
3. [stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/stock) - 242K (8 commits)
4. [mail](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/mail) - 191K (14 commits)
5. [mrp](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/mrp) - 178K (11 commits)
6. [resource](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/resource) - 169K (3 commits)
7. [base](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/base) - 158K (10 commits)
8. [hr_work_entry](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_work_entry) - 152K (5 commits)
9. [account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/account) - 137K (10 commits)
10. [google_calendar](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/google_calendar) - 118K (2 commits)
11. [hr](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr) - 115K (11 commits)
12. [hr_attendance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_attendance) - 110K (2 commits)
13. [payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/payment) - 103K (3 commits)
14. [sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale) - 96K (8 commits)
15. [pos_loyalty](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_loyalty) - 70K (1 commit)
16. [account_edi_ubl_cii](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/account_edi_ubl_cii) - 60K (1 commit)
17. [website](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/website) - 51K (4 commits)
18. [calendar](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/calendar) - 50K (3 commits)
19. [base_vat](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/base_vat) - 47K (1 commit)
20. [populate](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/populate) - 45K (1 commit)
21. [product](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/product) - 43K (2 commits)
22. [hr_holidays_attendance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_holidays_attendance) - 38K (1 commit)
23. [stock_account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/stock_account) - 34K (3 commits)
24. [mass_mailing](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/mass_mailing) - 33K (2 commits)
25. [pos_online_payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_online_payment) - 31K (1 commit)
26. [sale_project](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale_project) - 30K (1 commit)
27. [sale_management](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale_management) - 30K (3 commits)
28. [account_peppol](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/account_peppol) - 28K (2 commits)
29. [purchase](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/purchase) - 28K (2 commits)
30. [project](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/project) - 27K (4 commits)
31. [pos_self_order](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_self_order) - 26K (1 commit)
32. [crm](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/crm) - 25K (3 commits)
33. [im_livechat](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/im_livechat) - 25K (2 commits)
34. [sale_timesheet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale_timesheet) - 24K (1 commit)
35. [hr_recruitment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_recruitment) - 24K (3 commits)
36. [pos_restaurant](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_restaurant) - 23K (2 commits)
37. [maintenance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/maintenance) - 21K (3 commits)
38. [pos_hr](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_hr) - 18K (1 commit)
39. [purchase_requisition](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/purchase_requisition) - 14K (1 commit)
40. [sale_pdf_quote_builder](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale_pdf_quote_builder) - 12K (1 commit)
41. [pos_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_sale) - 12K (1 commit)
42. [fleet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/fleet) - 11K (2 commits)
43. [hr_fleet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_fleet) - 10K (3 commits)
44. [mass_mailing_sms](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/mass_mailing_sms) - 9.8K (2 commits)
45. [hr_maintenance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_maintenance) - 9.3K (2 commits)
46. [crm_iap_mine](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/crm_iap_mine) - 8.6K (1 commit)
47. [sale_purchase](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale_purchase) - 8.4K (1 commit)
48. [delivery](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/delivery) - 8.4K (1 commit)
49. [event_product](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/event_product) - 8.1K (1 commit)
50. [pos_bancontact_pay](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/pos_bancontact_pay) - 7.0K (1 commit)
51. [lunch](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/lunch) - 6.7K (1 commit)
52. [iap](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/iap) - 6.0K (1 commit)
53. [sale_stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/sale_stock) - 5.8K (2 commits)
54. [mail_plugin](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/mail_plugin) - 5.7K (1 commit)
55. [account_payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/account_payment) - 5.5K (1 commit)
56. [survey_crm](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/survey_crm) - 5.3K (1 commit)
57. [bus](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/bus) - 4.6K (1 commit)
58. [hr_expense](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/hr_expense) - 4.4K (1 commit)
59. [survey](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/survey) - 4.3K (1 commit)
60. [utm](https://github.com/akretion/odoo-module-diff-analysis/blob/main/20.0/utm) - 4.1K (1 commit)

In total: 71 addons, 199 data model
impacting commits, 3.6M of pseudo patches.

Generated by [odoo-module-diff](https://github.com/akretion/odoo-module-diff): these numbers are
heuristic (see the repo README); the full pseudo patches are in each
addon directory.
