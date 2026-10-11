# Gym Management Database

A relational database for a modern fitness gym, designed and implemented in Oracle SQL. The project takes the system from a written business description through conceptual modeling and normalization to a working schema, seeded data and a set of advanced queries.

It models the operational side of the gym (members, staff, subscriptions, personal training bookings, zones, equipment and maintenance) and the commercial side (product and subscription sales, receipts).

A full technical report (in Romanian) documents every design decision. See [`docs/`](docs/).

## Overview

| | |
|---|---|
| Database | Oracle (developed in Oracle SQL Developer) |
| Tables | 19 |
| Sequences | 12, used for primary key generation |
| Seed data | 179 `INSERT` statements with coherent sample data |
| Report | 17 sections, from requirements to denormalization |

## Domain Model

The gym is divided into **zones** (Fitness, Cardio, Pilates). Clients hold **subscriptions** (Standard, Gold, Diamond), and each subscription type grants access to one or more zones. Clients can also **book individual sessions** with personal trainers, and buy products or subscriptions, which produces a fiscal receipt. Technicians log **maintenance** on the equipment in each zone.

![Entity-Relationship Diagram](docs/er-diagram.png)

### Key design points

- **Staff specialization.** `ANGAJAT` is a supertype with three subtypes: `RECEPTIONER`, `TEHNICIAN` and `ANTRENOR`. Each subtype shares the employee's primary key.
- **Sales specialization.** `VANZARE` is specialized into `VANZARE_PRODUS` and `VANZARE_ABONAMENT`. Product sales link to product types through the associative table `INCLUDE`, which also stores quantity.
- **Access control.** The many-to-many relationship `ACCESEAZA` links subscription types to the zones they open: Standard covers Fitness, Gold covers Fitness and Cardio, and Diamond covers all zones.
- **Subscription history.** `DETINE_ABONAMENT_ACTIV` records which client holds which subscription type and for what period, so past and current subscriptions are both kept.
- **Bookings.** `REZERVA` ties together a client, a trainer and a session type, with the date and time.

### Tables

| Area | Tables |
|---|---|
| People | `CLIENT`, `ANGAJAT`, `RECEPTIONER`, `TEHNICIAN`, `ANTRENOR` |
| Gym space | `ZONA_SALA`, `ECHIPAMENT`, `MENTENANTA` |
| Subscriptions | `TIP_ABONAMENT`, `ACCESEAZA`, `DETINE_ABONAMENT_ACTIV` |
| Training sessions | `TIP_SEDINTA_INDIVIDUALA`, `REZERVA` |
| Sales | `VANZARE`, `VANZARE_PRODUS`, `VANZARE_ABONAMENT`, `TIP_PRODUS`, `INCLUDE`, `BON_FISCAL` |

Integrity is enforced with primary and foreign keys and `CHECK` constraints, for example positive salaries, positive zone capacity and positive session prices.

## What the Project Covers

**Design and theory**

- Requirements analysis and business rules
- Entity, relationship and attribute descriptions, with cardinalities and data types
- Entity-relationship diagram and conceptual diagram
- Relational schemas
- Normalization through 1NF, 2NF and 3NF, then BCNF, 4NF and 5NF
- Justified denormalization for a frequently run reporting query

**Implementation**

- Sequence-based key generation
- Table creation with constraints
- Coherent test data for every table

**Advanced SQL** (`queries.sql`)

| Topic | What is demonstrated |
|---|---|
| Correlated subqueries | Clients with Fitness bookings whose spending exceeds the per-client average, across 4 joined tables |
| Inline views | Per-product sales totals and revenue joined back to the product catalogue |
| Grouping with subqueries in `HAVING` | Receptionists whose sales total exceeds the average receptionist total |
| `NVL`, `DECODE`, ordering | A full sales report with defaults for missing clients and payment methods |
| String and date functions, `CASE`, `WITH` | A subscription statistics report with formatted names, `MONTHS_BETWEEN`, `TO_CHAR` and an active/expired status |
| DML with subqueries | Price increases, deletions and salary updates driven by subqueries |
| Complex view | A multi-table view with one permitted and one rejected DML operation, each explained |
| Outer join | Clients with their bookings, session types, zones and trainers, keeping clients that have no bookings |
| Relational division | Clients whose subscriptions give access to every zone in the gym |
| Top-N analysis | The 5 clients who spent the most |
| Query optimization | A query rewritten using relational algebra rules, with the algebraic expression, the algebraic tree and SQL before and after |

## Repository Structure

```
gym-management-database/
├── sql/
│   ├── schema_and_data.sql   # sequences, tables, constraints, seed data
│   └── queries.sql           # queries 12-17 from the report
├── docs/
│   ├── project-report.docx   # full technical report (Romanian)
│   └── er-diagram.png
└── README.md
```

## Getting Started

1. Open Oracle SQL Developer (or SQL*Plus) and connect to your database.
2. Run `sql/schema_and_data.sql` to create the sequences and tables and insert the sample data.
3. Run the statements in `sql/queries.sql` one section at a time and inspect the results.

Notes on running the queries:

- Sections 13 and 14 contain `UPDATE` and `DELETE` statements that change the seed data. Re-run the schema script to restore it.
- The last `INSERT` in section 14 is intentionally rejected. It demonstrates a DML operation that is not permitted on a complex view.

## Author

**Alexandra-Maria Bratan**
Computer Science student, University of Bucharest
[GitHub](https://github.com/alexandramariab)
