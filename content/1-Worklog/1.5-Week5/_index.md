---
title: "Week 5: 18/05 - 24/05"
date: 2026-07-10
weight: 5
chapter: false
---

## Week 5: Room CRUD Management & Booking Application APIs (18/05 - 24/05)

### Tasks Accomplished
*   Coded CRUD endpoints for managing dorm rooms (`/api/rooms`) letting admins add, edit, or delete rooms.
*   Developed API endpoints letting student tenants submit booking requests online (`/api/requests`).
*   Wrote validation logic checking field formats for email structures, national ID lengths, and phone numbers.

### Endpoint Details
*   `GET /api/rooms`: Retrieves all room lists with filters for availability.
*   `POST /api/rooms`: Creates a new room entity.
*   `POST /api/requests`: Tenant registers booking data, inserting records into `TenantRequests` at `PENDING` status.

### Outcomes
*   Core room-listing and booking submission APIs fully functional and tested locally via Swagger UI.
