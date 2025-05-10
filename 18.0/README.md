# Dude, what did they do to my Odoo at version 18.0?

You can see below the Odoo addons that got the largest data model changes between versions 17.0 and 18.0:
(this is just summing the size of the data model impacting commits addon per addon)
You can browse each directory to dig into the detail of these changes.

1. mrp - 488K
2. account - 376K
3. hr_holidays - 152K
4. point_of_sale - 132K
5. mail - 120K
6. sale - 116K
7. purchase - 96K
8. hr_recruitment - 96K
9. base - 92K
10. stock - 76K
11. sale_pdf_quote_builder - 76K
12. marketing_card - 76K
13. sale_timesheet - 72K
14. account_peppol - 72K
15. sale_project - 68K
16. microsoft_calendar - 40K
17. account_edi_ubl_cii - 40K
18. purchase_requisition - 32K
19. project - 32K
20. product - 28K
21. pos_self_order - 28K
22. pos_loyalty - 28K
23. hr_timesheet - 28K
24. hr - 28K
25. iap - 24K
26. hr_expense - 24K
27. google_calendar - 24K
28. event_sale - 24K
29. project_mrp - 20K
30. payment - 20K
31. mrp_account - 20K
32. hr_recruitment_skills - 20K
33. hr_attendance - 20K
34. event - 20K
35. cloud_storage_google - 20K
36. cloud_storage_azure - 20K
37. auth_signup - 20K
38. analytic - 20K
39. project_mrp_account - 16K
40. pos_restaurant - 16K
41. cloud_storage - 16K
42. account_tax_python - 16K
43. web_tour - 12K
44. web_editor - 12K
45. stock_delivery - 12K
46. snailmail_account - 12K
47. sale_mrp - 12K
48. pos_sale - 12K
49. pos_razorpay - 12K
50. hr_holidays_attendance - 12K
51. fleet - 12K
52. event_sms - 12K
53. event_booth_sale - 12K
54. bus - 12K
55. account_edi_proxy_client - 12K
56. account_check_printing - 12K
57. pos_paytm - 7.5K
58. pos_discount - 7.5K
59. calendar - 7.5K


```
command:  du -sh -- */ | sort -rh | head -n 70 | awk '{sub(/\/$/, "", $2); print NR ". " $2 " - " $1}'
```
