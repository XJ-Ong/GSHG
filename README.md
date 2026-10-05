# Global Stay Hotel Group (GSHG) – Hotel Reservation Database

**Module:** CT004-3-3-ADVBS Advanced Database Systems  
**Institution:** Asia Pacific University of Technology and Innovation  
**Platform:** Microsoft SQL Server  

A database system for managing hotel reservations across multiple countries: hotels, rooms, rates, guests, reservations, optional services, cancellations, changes, no-shows, billing, payments and room maintenance.

---

## Team

| Member | Themed area (queries) |
|--------|------------------------|
| 1 | Reservations and room availability |
| 2 | Guests and room occupancy |
| 3 | Room rates and optional services |
| 4 | Changes, cancellations and no-shows |
| 5 | Billing, payments and maintenance |

---

## What each member owns

Every member delivers the same set of items, so each person owns one file in each folder below.

| Item | Implementation (code) |
|------|-----------------------|
| Optimisation strategy | `optimisation/` |
| Constraint | `constraints/` |
| Stored procedure | `procedures/` |
| Trigger | `triggers/` |
| 7 SQL queries | `queries/` |


---

## Repository structure

```
GSHG/
├── README.md
├── schema/
│   ├── create_database.sql
│   └── create_tables.sql          # PKs, FKs, basic constraints
├── data/
│   └── seed_data.sql              # shared test data
├── constraints/                   # member-designed business-rule constraints
├── procedures/                    # member-designed stored procedures
├── triggers/                      # member-designed triggers
├── optimisation/                  # indexes, indexed views, etc.
├── queries/                       # 7 queries per member
└── docs/                          # design write-ups
```

File naming: `memberN_<item>.sql` (for example `member3_trigger.sql`).

---

## Setup

### Build the database (SSMS)

The database is **not** stored in Git. Only the scripts are. Everyone builds their own local copy by running the scripts by hand, in the order below.

1. Clone the repo and open SSMS. Connect to your local SQL Server instance.
2. Open the first script with **File > Open > File**.
3. Check the database selected in the toolbar dropdown matches the script (the `create_database.sql` script creates the database; every later script should run against it).

### Run order

| Step | Folder | Purpose | What to check afterwards |
|------|--------|---------|--------------------------|
| 1 | `schema` | Create database, then tables | Tables and foreign keys appear in Object Explorer |
| 2 | `constraints` | Add business-rule constraints | Constraints listed under each table |
| 3 | `procedures` | Create stored procedures | Procedures listed under Programmability |
| 4 | `triggers` | Create triggers | Triggers listed under each table |
| 5 | `data` | Load shared seed data | `SELECT` from each table returns rows, and no rule rejected the data |
| 6 | `optimisation` | Create indexes and other optimisations | Indexes listed under each table |
| 7 | `queries` | Queries (run individually as needed) | Results match what you expect from the seed data |

Within each folder, run the five member files in member order (`member1_...` to `member5_...`) unless a script says otherwise.

The database rules (constraints, procedures, triggers) depend only on the table structure, never on the data, so they are built before any data exists. The seed data is loaded last, with every rule already active.

### Starting over

All scripts should be **re-runnable** (use `DROP ... IF EXISTS` or `IF OBJECT_ID(...)` checks). To reset, run `create_database.sql` again, which should drop and recreate the database, then repeat the whole run order, since the new database starts empty.

---

## Script header template

Recommended to put this at the top of every script.

```sql
/*
 Member      : Member <1-5>
 Item        : <constraint | procedure | trigger | optimisation | query Q#.#>
 Purpose     : <what it does>
 Business rule supported : <which rule from the case study>
 Approach    : <how it works>
 Justification : <why this approach over the alternatives>
 Depends on  : <tables or objects it needs>
*/
```
