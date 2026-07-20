---
title: "5.4 Database Setup"
date: 2026-07-10
weight: 4
chapter: false
---

## 5.4 Relational Database Design & EF Core Configuration

A reliable student housing system demands a highly structured relational database to prevent data redundancy and ensure data integrity. In this lab, we use **PostgreSQL** running on **Amazon RDS** as our primary database engine, along with **Entity Framework Core (EF Core)** as the Object-Relational Mapper (ORM).

### 1. Database Entity Schema (ERD)

The SmartDorm database schema comprises the following core entities:
*   `Rooms`: Contains room information, rental rates, bed counts, and occupancy statuses (`Available`, `Occupied`, `Maintenance`).
*   `Tenants`: Houses student personal profiles, contact info, and paths to S3-hosted government ID uploads.
*   `Contracts`: Maps tenants (`TenantId`) to rooms (`RoomId`), enforcing contract durations and deposit amounts.
*   `UtilityUsages`: Captures monthly water and electricity meter readings (start and end values).
*   `Invoices`: Aggregates base room rent, calculated utility usage, and miscellaneous service fees.
*   `Vehicles` & `ParkingRegistrations`: Supports the parking pass registry and automated monthly parking billing.

---

### 2. DbContext Implementation (`AppDbContext.cs`)

The `AppDbContext` class serves as the bridge between C# application code and the PostgreSQL engine. It maps model classes to physical SQL tables, configuring enums to save as human-readable string values rather than default integers:

```csharp
using Microsoft.EntityFrameworkCore;
using SmartDorm.Api.Models;

namespace SmartDorm.Api.Data
{
    public class AppDbContext : DbContext
    {
        public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) {}

        // Entity sets mapping to PostgreSQL tables
        public DbSet<User> Users { get; set; } = null!;
        public DbSet<Tenant> Tenants { get; set; } = null!;
        public DbSet<Room> Rooms { get; set; } = null!;
        public DbSet<Contract> Contracts { get; set; } = null!;
        public DbSet<Invoice> Invoices { get; set; } = null!;
        public DbSet<UtilityUsage> UtilityUsages { get; set; } = null!;
        public DbSet<Vehicle> Vehicles { get; set; } = null!;
        public DbSet<ParkingRegistration> ParkingRegistrations { get; set; } = null!;

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            base.OnModelCreating(modelBuilder);

            // Convert enums to strings in the database
            modelBuilder.Entity<Room>()
                .Property(e => e.Status)
                .HasConversion<string>();

            modelBuilder.Entity<Contract>()
                .Property(e => e.Status)
                .HasConversion<string>();

            modelBuilder.Entity<Invoice>()
                .Property(e => e.Status)
                .HasConversion<string>();

            // Configure Foreign Key relationships (Fluent API)
            modelBuilder.Entity<Contract>()
                .HasOne(c => c.Room)
                .WithMany()
                .HasForeignKey(c => c.RoomId);

            modelBuilder.Entity<Contract>()
                .HasOne(c => c.Tenant)
                .WithMany()
                .HasForeignKey(c => c.TenantId);
        }
    }
}
```

---

### 3. Running Database Migrations

During development, you can set up the schema on your local PostgreSQL instance by installing the EF CLI and executing migrations:

#### Step 1: Install EF Core CLI globally
Run this command in your terminal:
```bash
dotnet tool install --global dotnet-ef
```

#### Step 2: Edit Local Connection String
Open [appsettings.Development.json](file:///m:/SmartDorm/backend/appsettings.Development.json) and point the connection string to your local PostgreSQL server:
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Database=smartdorm;Username=postgres;Password=YOUR_LOCAL_PASSWORD;Port=5432"
  }
}
```

#### Step 3: Create Migration Code
From the `backend/` directory, create a migration script that parses C# models into migration classes:
```bash
dotnet ef migrations add InitialCreate
```

#### Step 4: Apply Schema to Database
Update your database schema to match the generated migration script:
```bash
dotnet ef database update
```
*(Alternatively, you can run the raw SQL schema script [database.sql](file:///m:/SmartDorm/database.sql) using pgAdmin or psql command line tool).*
