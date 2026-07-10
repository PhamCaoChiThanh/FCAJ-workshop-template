---
title: "Week 3: 04/05 - 10/05"
date: 2026-07-10
weight: 3
chapter: false
---

## Week 3: SmartDorm Kickoff & Relational Database Design (04/05 - 10/05)

### Tasks Accomplished
*   Conducted project kickoff meeting to lock down the individual project idea: **SmartDorm** (Smart Dormitory Management System).
*   Analyzed core functionalities: Room indexing, student booking applications, contract generation, and monthly utility indexing.
*   Designed the database Entity Relationship Diagram (ERD) containing: Room, Tenant, Contract, Invoice, UtilityUsage, Maintenance, and Vehicle entities.
*   Created Git repositories and initialized local developer workstations.

### Database Design Technical Details
We designed the relational PostgreSQL database schema:
*   `Rooms` table stores room numbers, prices, capacity, and current state (Vacant/Occupied).
*   `Tenants` table stores personal information (names, national ID cards, phone numbers).
*   `Contracts` table establishes relationships between `Rooms` and `Tenants` with active timestamps.
*   `Invoices` table tracks billing parameters (base price, utility usage, extra charges).
*   `UtilityUsage` table captures electricity (kWh) and water (m3) consumption monthly per room.

### Outcomes
*   Completed the database ERD and schema design.
*   GitHub repositories successfully initialized and ready for development.
