---
title: "Week 7: 01/06 - 07/06"
date: 2026-07-10
weight: 7
chapter: false
---

## Week 7: Utility Tracking Indexes & Monthly Billing Calculations (01/06 - 07/06)

### Tasks Accomplished
*   Designed the monthly utility index tracking schema (UtilityUsage).
*   Implemented calculations for room rent, electricity usage, water usage, and general service fees inside the monthly Invoice generation flow.
*   Created endpoints to fetch and update monthly utility records.

### Billing Algorithm Formula
Total invoice amount is calculated dynamically:
```text
Total = Base Room Price
        + (Ending Electricity - Beginning Electricity) * Unit Electricity Price
        + (Ending Water - Beginning Water) * Unit Water Price
        + Standard Monthly Service Fees
```
Initial status is marked as `UNPAID` and flips to `PAID` once confirmed by bank transfer notifications.

### Outcomes
*   The system accurately automates monthly utility calculations per room, avoiding manual arithmetic errors.
