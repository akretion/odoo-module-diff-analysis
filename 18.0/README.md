# Dude, what did they do to my Odoo at version 18.0?

You can see below the Odoo addons that got the largest data model
changes between versions 17.0 and 18.0:
(this is just summing the size of the data model impacting commits
addon per addon; method signature deltas are in each addon's
method_signatures.patch)
You can browse each directory to dig into the detail of these changes.

1. [mrp](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/mrp) - 480K (3 commits)
2. [account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/account) - 353K (10 commits)
3. [hr_holidays](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/hr_holidays) - 135K (8 commits)
4. [point_of_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/point_of_sale) - 113K (6 commits)
5. [mail](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/mail) - 111K (5 commits)
6. [sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/sale) - 103K (5 commits)
7. [purchase](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/purchase) - 89K (3 commits)
8. [hr_recruitment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/hr_recruitment) - 87K (2 commits)
9. [base](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/base) - 77K (5 commits)
10. [marketing_card](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/marketing_card) - 69K (2 commits)
11. [sale_pdf_quote_builder](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/sale_pdf_quote_builder) - 68K (2 commits)
12. [stock](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/stock) - 67K (3 commits)
13. [sale_timesheet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/sale_timesheet) - 64K (2 commits)
14. [account_peppol](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/account_peppol) - 62K (3 commits)
15. [sale_project](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/sale_project) - 59K (2 commits)
16. [account_edi_ubl_cii](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/account_edi_ubl_cii) - 32K (2 commits)
17. [microsoft_calendar](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/microsoft_calendar) - 30K (2 commits)
18. [purchase_requisition](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/purchase_requisition) - 26K (1 commit)
19. [project](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/project) - 24K (2 commits)
20. [hr_timesheet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/hr_timesheet) - 22K (1 commit)
21. [product](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/product) - 20K (2 commits)
22. [pos_loyalty](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/pos_loyalty) - 20K (2 commits)
23. [hr](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/hr) - 18K (3 commits)
24. [iap](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/iap) - 17K (1 commit)
25. [google_calendar](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/google_calendar) - 17K (1 commit)
26. [event_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/event_sale) - 16K (2 commits)
27. [hr_attendance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/hr_attendance) - 16K (1 commit)
28. [hr_recruitment_skills](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/hr_recruitment_skills) - 16K (1 commit)
29. [pos_self_order](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/pos_self_order) - 15K (3 commits)
30. [auth_signup](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/auth_signup) - 15K (1 commit)
31. [mrp_account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/mrp_account) - 15K (1 commit)
32. [cloud_storage_azure](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/cloud_storage_azure) - 14K (1 commit)
33. [project_mrp](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/project_mrp) - 14K (1 commit)
34. [analytic](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/analytic) - 13K (1 commit)
35. [hr_expense](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/hr_expense) - 13K (2 commits)
36. [cloud_storage_google](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/cloud_storage_google) - 12K (1 commit)
37. [event](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/event) - 12K (1 commit)
38. [pos_restaurant](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/pos_restaurant) - 12K (1 commit)
39. [project_mrp_account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/project_mrp_account) - 11K (1 commit)
40. [payment](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/payment) - 11K (2 commits)
41. [account_tax_python](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/account_tax_python) - 11K (1 commit)
42. [cloud_storage](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/cloud_storage) - 10K (1 commit)
43. [snailmail_account](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/snailmail_account) - 7.9K (1 commit)
44. [web_tour](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/web_tour) - 7.4K (1 commit)
45. [account_edi_proxy_client](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/account_edi_proxy_client) - 7.2K (1 commit)
46. [stock_delivery](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/stock_delivery) - 7.1K (1 commit)
47. [event_booth_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/event_booth_sale) - 6.9K (1 commit)
48. [fleet](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/fleet) - 6.9K (1 commit)
49. [bus](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/bus) - 6.7K (1 commit)
50. [pos_sale](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/pos_sale) - 6.4K (1 commit)
51. [sale_mrp](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/sale_mrp) - 5.7K (1 commit)
52. [account_check_printing](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/account_check_printing) - 5.6K (1 commit)
53. [pos_razorpay](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/pos_razorpay) - 5.5K (2 commits)
54. [hr_holidays_attendance](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/hr_holidays_attendance) - 5.1K (1 commit)
55. [web_editor](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/web_editor) - 5.0K (1 commit)
56. [event_sms](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/event_sms) - 5.0K (1 commit)
57. [pos_discount](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/pos_discount) - 2.6K (1 commit)
58. [calendar](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/calendar) - 2.5K (1 commit)
59. [pos_paytm](https://github.com/akretion/odoo-module-diff-analysis/blob/main/18.0/pos_paytm) - 2.2K (1 commit)

In total: 59 addons, 118 data model
impacting commits, 2.4M of pseudo patches.

Generated by [odoo-module-diff](https://github.com/akretion/odoo-module-diff): these numbers are
heuristic (see the repo README); the full pseudo patches are in each
addon directory.
