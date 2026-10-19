# GSHG Database Design

> **Purpose.** Turns `docs/brainstorm.md` and the case study into the final design: merged entities, 3NF normalisation, and the full ERD.
> **Inputs:** `docs/brainstorm.md` (Sections 1-5) and the assignment case study.
> **Out of scope:** constraints, stored procedures, triggers and optimisation (these live in their own folders). Ideas for them found while merging go in the Decision Log (Section 10).

---

## 0. How this file works

| Item | Value |
|---|---|
| Design owner (does the merge and writes the final tables) | `<name>` |
| Reviewers | `<names>` (every section needs at least one reviewer other than the owner) |
| Design freeze date (after this, schema changes need group agreement) | `<YYYY-MM-DD>` |
| Final submission date | 3 January 2027 |

### Workflow

1. Owner fills Sections 4 to 9 in order. Sections 2 and 3 are pre-filled and are extended as the group finds more.
2. Members review the entities that touch their own queries. Disagreements go into the Decision Log (Section 10), not into silent edits.
3. Once Section 7 and 8 are signed off, the design is frozen and `schema/create_tables.sql` is written from it.

### Section status tracker

| Section | Status (`Not started` / `Draft` / `In review` / `Signed off`) | Reviewed by |
|---|---|---|
| 1 Sources and scope | Draft | |
| 2 Case-study rule extraction | Draft | |
| 3 Query-implied data | Draft | |
| 4 Merged entity registry | Not started | |
| 5 Conceptual ERD | Not started | |
| 6 Normalisation | Not started | |
| 7 Final entities | Not started | |
| 8 Logical ERD | Not started | |
| 9 Denormalisation decisions | Not started | |
| 10 Decision log | Ongoing | |

---

## 1. Sources and scope

- **Query side:** the 35 individual queries, captured per member in `docs/brainstorm.md`.
- **Business side:** the case study (hotels, rooms, rates, reservations, services, cancellations/changes/no-shows, check-in/out, billing, payments, maintenance).
- **Target platform:** Microsoft SQL Server.
- **Standard:** 3NF or higher; any denormalisation must be justified (Section 9).
- **Required output:** entities and relationships, PKs and FKs, cardinality, optionality.

Assumptions that affect the whole design (add as agreed):

| # | Assumption | Agreed on |
|---|---|---|
| A1 | | |

---

## 2. Case-study rule extraction

Every business statement in the case study is turned into the data needed to support it. "Candidate data" is a starting suggestion only; the real entities are decided in Section 4. **Type:** `S` = structural (needs tables/columns), `R` = rule (enforced later by constraint/procedure/trigger, but may still need columns here).

| ID | Rule (paraphrased from the case study) | Type | Candidate data it implies | Status |
|---|---|---|---|---|
| R1 | Group has hotels in several countries; head office is in Kuala Lumpur | S | Hotel (name, country, city/address) | |
| R2 | Rooms belong to categories: Standard, Deluxe, Family, Suite | S | RoomCategory; Room (hotel, room number, category) | |
| R3 | Rates vary by hotel, room category and stay date | S | Rate/price table keyed by hotel + category + date (or date range) | |
| R4 | Each room has a maximum occupancy | S | Room or RoomCategory max occupancy | |
| R5 | Accessible rooms can be requested, assigned only if available | S+R | Room accessibility flag; reservation accessibility request | |
| R6 | Rooms under maintenance cannot be assigned during the maintenance period | S+R | RoomMaintenance (room, start, expected end, actual end?) | |
| R7 | One reservation can contain one or more rooms | S | Reservation; reservation-room detail (category booked, room assigned, stay dates) | |
| R8 | Every person staying in a room must be recorded | S | Guest/person with date of birth; room-occupant link | |
| R9 | At least one adult (18+) per occupied room | R | Date of birth + stay date to derive age | |
| R10 | Child under 12 pays reduced rate; infant under 2 free if no extra bed | S+R | Age bands; child rate or discount; extra-bed flag per occupant | |
| R11 | Extra beds and baby cots: subject to availability, may be charged | S+R | Extra item catalogue, per-hotel stock, charge, request per room | |
| R12 | Hotels offer optional services (airport transfer, breakfast, parking, spa) | S | Service catalogue; per-hotel service offering with price | |
| R13 | Services charged per person, per room or per reservation | S | Charging basis; quantity; link to what it applies to | |
| R14 | Reservation status: Pending, Confirmed, Checked In, Checked Out, Cancelled | S | Status (lookup or constrained column), status history? | |
| R15 | A room cannot be assigned to overlapping active stays | R | Stay start/end at room-assignment level | |
| R16 | Guests may change stay dates or room category, subject to availability and price difference | S+R | Change record: old and new dates/category, price difference, change date | |
| R17 | Cancellation < 48 hours before scheduled check-in costs the first night's rate | S+R | Scheduled check-in datetime; cancellation datetime, charge | |
| R18 | Guest not arrived by hotel's check-in deadline is a no-show, charged per the reservation's rate plan | S+R | Hotel check-in deadline; RatePlan (incl. no-show charge rule); no-show record | |
| R19 | Check-in and check-out are recorded | S | Actual check-in and check-out datetimes per stay | |
| R20 | Final bill: room charges, optional services, discounts, additional charges | S | Bill and bill lines (or equivalent), Discount, additional charge type | |
| R21 | Payments and refunds recorded so outstanding balance can be determined | S | Payment (amount, method, date, payment vs refund) | |

Add new rows below as the group finds more rules.

---

## 3. Query-implied data

Data that a query needs but that the case study does not state directly, or that is easy to miss. Source column uses the brainstorm IDs (`M<member>-Q<number>`).

| # | Implied requirement | Source | Design consequence to decide |
|---|---|---|---|
| Q1 | Guest ages are needed, and age bands depend on the stay date | M2-Q2, M2-Q5, R9, R10 | Store date of birth; age is derived, not stored |
| Q2 | Original and revised stay dates for changed reservations | M4-Q2 | Needs change history, not only the current reservation row |
| Q3 | "Currently under maintenance" needs a start and expected end date | M5-Q3 | RoomMaintenance dates |
| Q4 | No-show charge depends on the reservation's rate plan | R18, M4-Q4 | Rate plan must exist as an entity or attribute |
| Q5 | Overlap checks and "completed stays" need stay dates per assigned room | M1-Q1, M5-Q4, R15 | Stay dates at room-assignment level, with actual check-in/out |
| Q6 | Textual status description for every reservation | M1-Q3 | Status lookup or CASE expression; decide which |
| Q7 | Services that were never purchased | M3-Q3 | Service catalogue must be separate from purchases |
| Q8 | Cancellation rate needs a denominator (total reservations per hotel per year) | M4-Q6 | Cancellation date or status must be reliably queryable |
| Q9 | Revenue from completed stays, payments by method | M5-Q5, M5-Q6 | Bill/payment link to hotel, reservation and payment method |
| Q10 | Promo-discounted rate is a calculated column | M3-Q1 | Do not store the discounted rate |
| Q11 | Charging basis and calculated charge per service | M3-Q2 | Decide whether the charge is stored or derived (see Section 9) |

Add new rows below.

---

## 4. Merged entity registry

One row per canonical entity, built from `brainstorm.md` Sections 1-5 plus Sections 2 and 3 above. Merge attributes, record who needs them, and note conflicts.

| Entity (canonical) | Meaning | Sources (M#, R#, Q#) | Merged attributes (with source Q/R) | Conflicts to resolve |
|---|---|---|---|---|
| `<Entity>` | | | | |

### 4.1 Conflict log

| # | Conflict (naming, meaning, type, scope) | Entities / members | Options | Decision | Decided on |
|---|---|---|---|---|---|
| C1 | | | | | |

---

## 5. Conceptual ERD

Entities and relationships only, **no normalisation yet**. This shows the group's shared picture of the business before any restructuring.

### 5.1 Entity list

| Entity | One-line description |
|---|---|
| | |

### 5.2 Relationship list

| Entity A | Relationship (verb) | Entity B | Cardinality | Optionality (each side) | Source |
|---|---|---|---|---|---|
| | | | | | |

### 5.3 Relationship matrix

At-a-glance view of the 5.2 list. Rows and columns are the same entities; each cell is the cardinality of the relationship **from the row entity to the column entity** (`1:1`, `1:N`, `N:1`, `M:N`), or blank if none. Put the verb in brackets if two entities have more than one relationship.

| | Entity A | Entity B | Entity C |
|---|---|---|---|
| **Entity A** | | | |
| **Entity B** | | | |
| **Entity C** | | | |

---

## 6. Normalisation

One block per entity. Show the work: the marker needs to see how 3NF was reached, not just the result.

### 6.1 Normalisation block template

> #### `<Entity or relation group>`
>
> **Starting attributes (from Section 4):** `...`
>
> **Candidate key(s):** `...`
>
> **Functional dependencies:**
> - `{...} -> {...}`
>
> | Form | Check | Issue found | Action |
> |---|---|---|---|
> | 1NF | Atomic values, no repeating groups | | |
> | 2NF | No partial dependency on part of a composite key | | |
> | 3NF | No transitive dependency (non-key -> non-key) | | |
>
> **Resulting relations:** `Relation1(PK, ...)`, `Relation2(PK, ..., FK)`

### 6.2 Normalisation work

<!-- Add one block per entity or related group here. -->

---

## 7. Final entities

One card per final relation, produced from Section 6. This becomes the source for `schema/create_tables.sql`.

### 7.1 Card format

> #### `TableName`
> *What a row represents.*
>
> | Column | Type | Null? | Key | References | Notes |
> |---|---|---|---|---|---|
> | TableNameID | INT | No | PK | | |
> | ParentID | INT | No | FK | Parent.ParentID | |

### 7.2 Final entities

<!-- Add cards here. Keep alphabetical or by area (Hotel/Room, Guest, Reservation, Services, Billing, Maintenance). -->

### 7.3 Table index

| # | Table | Area | Owner of the table design | Reviewed |
|---|---|---|---|---|
| 1 | | | | |

---

## 8. Logical ERD

The final ERD is shown in table form. Entities, attributes, PKs and FKs are in Section 7; this section adds the relationships, cardinality and optionality.

### 8.1 Notation

Each side of a relationship is written as `min..max`, meaning how many rows of that table can be linked to one row on the other side.

| Notation | Meaning |
|---|---|
| `1..1` | exactly one (mandatory) |
| `0..1` | zero or one (optional) |
| `1..*` | one or many (mandatory) |
| `0..*` | zero or many (optional) |

Read each row as two sentences. Example (illustration only, not a real answer): a Hotel has `0..*` Rooms; a Room belongs to `1..1` Hotel.

### 8.2 Relationship table

| # | Parent table | Child table | FK column (in child) | Verb | Child per parent | Parent per child | Notes |
|---|---|---|---|---|---|---|---|
| L1 | Hotel | Room | Room.HotelID | has | 0..* | 1..1 | |

For an M:N relationship, list it as two rows, one for each side of its junction table.

### 8.3 Relationship matrix

Same format as Section 5.3, but using the final tables and `min..max` notation. Each cell shows the child-per-parent side of the relationship from the row table to the column table.

| | Table A | Table B | Table C |
|---|---|---|---|
| **Table A** | | | |
| **Table B** | | | |
| **Table C** | | | |

### 8.4 Consistency check

- [ ] Every FK column in Section 7 appears once in 8.2
- [ ] Every row in 8.2 has a matching FK in Section 7
- [ ] No table is unconnected unless that is intended
- [ ] Optionality in 8.2 matches `Null?` of the FK column in Section 7 (mandatory = `No`, optional = `Yes`)

---

## 9. Denormalisation decisions

Default is 3NF. Any exception must be listed here with a reason, since the brief requires justification. Typical candidates: storing the rate or charge that was applied at booking/billing time, because rate tables change but past bills must not.

| # | Table.Column (or structure) | What is duplicated or derived | Why (performance, history, auditability) | Risk and how it is kept consistent | Agreed by |
|---|---|---|---|---|---|
| DN1 | | | | | |

---

## 10. Decision log

Every design decision that was argued about, and ideas that belong to later stages.

| # | Date | Decision | Alternatives considered | Reason | Decided by |
|---|---|---|---|---|---|
| D1 | | | | | |

### 10.1 Ideas for later stages (parking lot)

- `[constraint]` 
- `[procedure]` 
- `[trigger]` 
- `[optimisation]` 
