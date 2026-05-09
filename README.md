# 🚖 RideFlow DB

> A full-stack SQL Server database system for ride-hailing operations — from a normalized OLTP schema to an analytical Data Warehouse, ETL pipeline, and business intelligence queries.

---

## 📌 Overview

**RideFlow DB** simulates the complete data infrastructure of a ride-hailing platform (like Uber or Careem). The project covers the entire data lifecycle:

1. **Database Design** — EERD diagram + 3NF normalization
2. **OLTP Implementation** — operational SQL Server database
3. **Core Queries** — CRUD, JOINs, transactions
4. **Data Warehouse** — star-schema OLAP database
5. **ETL Pipeline** — loading operational data into the warehouse
6. **Analytics** — window functions and KPI reports

---

## 🗂️ Project Structure

```
RideFlow-DB/
│
├── 01_OLTP_Database/
│   ├── RideHailing_SQLServer.sql      # Full schema + sample data (13 tables)
│   ├── BasicQueries.sql               # SELECT, JOIN, aggregate queries
│   ├── Transactions.sql               # ACID-compliant transactions
│   └── EERD/
│       ├── RideHailing_ER_Diagram.png # Visual ER diagram
│       └── RideHailing_ER_Diagram.drawio
│
├── 02_Optimization/
│   └── (Index & query optimization screenshots)
│
├── 03_Stored_Procedures/
│   └── (Stored procedure execution screenshots)
│
└── 04_Data_Warehouse/
    ├── RideHailingDW.sql              # Star-schema warehouse (Dims + Fact)
    ├── RideHailingETL.sql             # ETL pipeline: OLTP → DW
    ├── FactTable.sql                  # Fact table population
    └── Analytical_Queries.sql        # Window functions & KPI reports
```

---

## 🏗️ Database Design — OLTP

### Schema: 13 Tables in 3NF

| Table | Type | Description |
|---|---|---|
| `User` | Supertype | Base entity for all users |
| `UserPhone` | Multi-valued | Phone numbers for users |
| `Rider` | Subtype | Inherits from User |
| `Admin` | Subtype | Inherits from User |
| `AdminPermissions` | Multi-valued | Permissions per admin |
| `Driver` | Supertype | Independent driver entity |
| `DriverPhone` | Multi-valued | Phone numbers for drivers |
| `FullTimeDriver` | Subtype | Salaried drivers |
| `PartTimeDriver` | Subtype | Flexible-hours drivers |
| `Request` | Core | Ride request (Pending/Accepted/Rejected) |
| `Trip` | Core | Actual trip linked to request + driver |
| `Payment` | Core | Payment per trip |
| `TripFeedback` | Weak Entity | Ratings and comments per trip |

### Design Highlights
- **Supertype/Subtype hierarchies** — Disjoint, Total participation
- **Multi-valued attributes** stored in separate junction tables
- **Derived attributes** (Age, Rating) computed at query time — not stored
- **CHECK constraints** on Status, PaymentMethod, Salary
- **Cascade deletes** preserve referential integrity

---

## 🏭 Data Warehouse — OLAP

### Star Schema

```
          DimDate
             |
DimRider ── FactTrip ── DimDriver
             |
         DimLocation
```

| Table | Role |
|---|---|
| `FactTrip` | Central fact table (Amount, Duration, Rating) |
| `DimDriver` | Driver dimension |
| `DimRider` | Rider dimension |
| `DimDate` | Date dimension (Day, Month, Quarter, Year) |
| `DimLocation` | Pickup & dropoff locations |

---

## 🔄 ETL Pipeline

Data flows from the operational database into the warehouse in three steps:

```
RideHailingDB (OLTP)  →  Transform & Clean  →  RideHailingDW (OLAP)
```

- Loads dimension tables first (Slowly Changing Dimensions handled)
- Populates `FactTrip` by joining Trip + Payment + Feedback
- Handles NULL end times for ongoing trips

---

## 📊 Analytical Queries

Key reports built with Window Functions:

- **Top 5 Drivers** by average revenue and rating (`RANK`)
- **Revenue by Payment Method** — Cash vs Card vs Wallet
- **Most Popular Pickup Locations**
- **Monthly Trip Trends** using `DimDate`
- **Driver Performance Over Time** using `LAG` / `LEAD`

---

## 🗃️ Sample Data

| Entity | Count |
|---|---|
| Riders | 20 |
| Drivers | 20 (11 full-time, 9 part-time) |
| Requests | 30 (20 Accepted, 5 Pending, 5 Rejected) |
| Trips | 20 (17 Completed, 3 Ongoing) |
| Payments | 20 |
| Feedback Records | 30 |

---

## ⚙️ How to Run

### Prerequisites
- SQL Server 2019+ or SQL Server Express
- SSMS (SQL Server Management Studio)

### Steps

**1. Run the OLTP database:**
```sql
-- Open in SSMS and execute:
01_OLTP_Database/RideHailing_SQLServer.sql
```

**2. Run queries:**
```sql
01_OLTP_Database/BasicQueries.sql
01_OLTP_Database/Transactions.sql
```

**3. Create the Data Warehouse:**
```sql
04_Data_Warehouse/RideHailingDW.sql
```

**4. Run the ETL pipeline:**
```sql
04_Data_Warehouse/RideHailingETL.sql
04_Data_Warehouse/FactTable.sql
```

**5. Run analytics:**
```sql
04_Data_Warehouse/Analytical_Queries.sql
```

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| SQL Server (T-SQL) | Database engine |
| SSMS | Development environment |
| draw.io | EERD diagram |
| Stored Procedures | Business logic encapsulation |
| Window Functions | Analytical reporting |

---

## 📄 License

This project is open-source and available under the [MIT License](LICENSE).
