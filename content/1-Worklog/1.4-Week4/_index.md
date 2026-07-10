---
title: "Week 4: 11/05 - 18/05"
date: 2026-07-10
weight: 4
chapter: false
---

## Week 4: Backend Architecture Setup (ASP.NET Core) & Local PostgreSQL Migration (11/05 - 18/05)

### Tasks Accomplished
*   Initialized the backend project using ASP.NET Core Web API (.NET 10).
*   Structured directories matching MVC / Clean Architecture principles:
    *   `Controllers`: Receives HTTP requests and routes actions.
    *   `Models`: Defines entities mapped to database tables.
    *   `Services`: Processes business logic algorithms.
*   Installed Entity Framework Core (EF Core) and configured DbContext.
*   Set up a local PostgreSQL container using Docker for local dev database instance.

### Code Details
*   Configured secured connection strings within `appsettings.json`.
*   Wrote data seeders to populate initial room layouts automatically during startup.
*   Executed migration commands:
    ```bash
    dotnet ef migrations add InitialCreate
    dotnet ef database update
    ```

### Outcomes
*   Backend Web API architecture up and running locally.
*   PostgreSQL database successfully provisioned locally on Docker with complete tables.
