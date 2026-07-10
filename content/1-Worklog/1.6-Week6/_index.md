---
title: "Week 6: 25/05 - 31/05"
date: 2026-07-10
weight: 6
chapter: false
---

## Week 6: Booking Approval Flow & Automated Contract Generation (25/05 - 31/05)

### Tasks Accomplished
*   Developed application approval APIs for managers (transitioning status from PENDING to APPROVED or REJECTED).
*   Implemented backend logic to automatically generate a corresponding Contract entity immediately when a booking application is approved.
*   Configured auto-updating of room status to reflect occupancy changes (setting status to OCCUPIED) to prevent double bookings.

### Automation Logic Detail
On Admin approval action:
1.  Updates the booking request status to `APPROVED`.
2.  Sets contract start date to current day, and end date to exactly one year later.
3.  Inserts a new Contract record with generated identifiers.
4.  Switches room state properties from `VACANT` to `OCCUPIED`.

### Outcomes
*   Automated leasing workflow and lease contract lifecycle fully established on Backend.
