# GSHG Brainstorm: Minimum Entities and Attributes

> **Purpose.** Stage 1 of the design. Each member lists the *minimum* entities and attributes needed to answer their own 7 queries. Merging the five sections and covering the rest of the case study will be done later in a separate file. Normalisation (3NF) and the final ERD come **after** this file, not in it.

> **Out of scope here:** constraints, stored procedures, triggers and optimisation. Do not design them in this file. If an idea occurs to you, drop it in the *Ideas parking lot* of your section so it is not lost.

***

## 0. How to use this file

### Ground rules

1. **Edit only your own section** (Section 1 to 5 for Members 1 to 5). Sections are fenced by `<!-- SECTION n START/END -->` comments; keep your edits between them. This keeps Git merges conflict-free.
2. **Minimum means minimum.** Add an attribute only if a query needs it. Every attribute must have a **Needed by** reference such as `M3-Q2`. Attributes added on a hunch go in the Ideas parking lot, not in an entity table.
3. **Overlap between members is expected.** `Reservation`, `Room`, `Hotel` will appear in several sections. Use the same entity name as other members where the concept is the same, and list only the attributes *your* queries need. Do not try to deduplicate; that happens in the later merge.
4. **Think in business terms, not tables.** Do not add junction tables or surrogate IDs just to look finished. If a query needs "which guests are in which room", write the entity that holds that fact and let normalisation sort it out later.
5. **Work question by question.** Fill in the Question Trace first (what data does each query touch?), then derive entities from it.
6. **Status** at the top of your section is one of `Not started`, `In progress` or `Done`. Update it as you go.

### Naming conventions

| Thing             | Convention                                             | Example                                    |
| ----------------- | ------------------------------------------------------ | ------------------------------------------ |
| Entity            | Singular, PascalCase                                   | `Hotel`, `RoomCategory`, `OptionalService` |
| Attribute         | PascalCase; PK is `<Entity>ID`                         | `HotelID`, `CheckInDate`                   |
| Foreign key       | Same name as the PK it points to                       | `Reservation.HotelID`                      |
| Booleans          | `Is...` prefix                                         | `IsAccessible`                             |
| Dates / datetimes | `...Date` for date only, `...DateTime` for date + time | `CheckInDate`, `CancelledDateTime`         |
| Money             | `...Amount`, `...Rate`                                 | `NightlyRate`, `PaymentAmount`             |
| Status values     | Listed in Notes as an enumeration                      | `Pending, Confirmed, ...`                  |

### Key column legend

| Code                | Meaning                                                                                                                                                            |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `PK`                | Primary key (or part of a composite PK, mark each part `PK`)                                                                                                       |
| `FK -> Entity.Attr` | Foreign key                                                                                                                                                        |
| `UK`                | Candidate / unique key                                                                                                                                             |
| `D`                 | **Derived**: can be calculated from other attributes (for example total nights). Listed so we know the query needs it, but it will probably *not* be stored (3NF). |
| `-`                 | Ordinary attribute                                                                                                                                                 |

### Entity card format (copy this for each entity)

```Markdown
#### Entity: EntityName
*One line: what a row represents.*

| Attribute | Suggested type | Key | Needed by | Notes |
|---|---|---|---|---|
| EntityNameID | INT | PK | M1-Q1 | |
| SomeAttribute | VARCHAR(50) | - | M1-Q2 | allowed values / rules / doubts |
```

### Relationship table format

| Entity A | Relationship (verb) | Entity B | Cardinality | Optionality                                            | Needed by |
| -------- | ------------------- | -------- | ----------- | ------------------------------------------------------ | --------- |
| Hotel    | has                 | Room     | 1 : N       | Hotel must have >= 1 Room? Room must belong to 1 Hotel | M1-Q1     |

Cardinality is `1:1`, `1:N` or `M:N`. If you write `M:N`, say what the relationship itself carries (for example quantity), because that tells us what the junction entity needs.

### Worked example (illustration only, not a real answer)

**Question trace row:**

| Q     | Query needs (in plain words)                                | Entities touched                        | Attributes needed                                                                          |
| ----- | ----------------------------------------------------------- | --------------------------------------- | ------------------------------------------------------------------------------------------ |
| M1-Q2 | Confirmed reservations at a hotel with guest name and dates | Reservation, Guest, Hotel, RoomCategory | Reservation: status, check-in, check-out; Guest: name; Hotel: identity; RoomCategory: name |

**Entity card:**

#### Entity: Guest *(example)*

*A person who makes or stays in a reservation.*

| Attribute | Suggested type | Key | Needed by | Notes                                 |
| --------- | -------------- | --- | --------- | ------------------------------------- |
| GuestID   | INT            | PK  | M1-Q2     |                                       |
| FullName  | VARCHAR(100)   | -   | M1-Q2     | split into first/last later if needed |

## Recommended starting entities (from the case study only)

**Recommended Entities from Case Study:**

* Hotel
* RoomCategory
* Room
* RoomRate
* Guest
* Reservation
* RoomAssignment
* OptionalService
* ReservationService
* Payment
* Maintenance

<!-- SECTION 1 START: MEMBER 1 -->

## Section 1: Member 1 - Reservations and room availability

**Status:** `Not started`

### 1.1 Question trace

Fill the last two columns for every query before drawing entities.

| Q           | Query (short)                                                                                                            | Entities touched | Attributes needed |
| ----------- | ------------------------------------------------------------------------------------------------------------------------ | ---------------- | ----------------- |
| M1-Q1 (i)   | Available rooms for a hotel, check-in, check-out, room category; exclude overlapping active reservations and maintenance |                  |                   |
| M1-Q2 (ii)  | Confirmed reservations at a hotel: reservation ID, guest name, room category, check-in, check-out                        |                  |                   |
| M1-Q3 (iii) | Every reservation with a textual description of its status                                                               |                  |                   |
| M1-Q4 (iv)  | Most frequently booked room category at a hotel in a date range                                                          |                  |                   |
| M1-Q5 (v)   | Confirmed reservation count by hotel and room category, with subtotals and grand total (ROLLUP/CUBE)                     |                  |                   |
| M1-Q6 (vi)  | Hotels with no confirmed reservations in a specified month                                                               |                  |                   |
| M1-Q7 (vii) | Extra query: reservation demand                                                                                          |                  |                   |

### 1.2 Entities and attributes

<!-- Copy the entity card format from Section 0 for each entity. One card per entity. -->

#### Entity: <Name>

*<what a row represents>*

| Attribute | Suggested type | Key | Needed by | Notes |
| --------- | -------------- | --- | --------- | ----- |
|           |                |     |           |       |

### 1.3 Relationships

| Entity A | Relationship (verb) | Entity B | Cardinality | Optionality | Needed by |
| -------- | ------------------- | -------- | ----------- | ----------- | --------- |
|          |                     |          |             |             |           |

### 1.4 Assumptions and open questions

| #   | Type                  | Item | Raised for (Q or entity) | Resolution |
| --- | --------------------- | ---- | ------------------------ | ---------- |
| 1.1 | Assumption / Question |      |                          |            |

### 1.5 Ideas parking lot (optional)

Free-form. Anything that is not an entity or attribute: potential constraints, procedures, triggers, optimisation ideas, query tricks, denormalisation thoughts. Tag each so it can be picked up later.

* `[constraint]`
* `[procedure]`
* `[trigger]`
* `[optimisation]`
* `[other]`

<!-- SECTION 1 END -->

***

<!-- SECTION 2 START: MEMBER 2 -->

## Section 2: Member 2 - Guests and room occupancy

**Status:** `Not started`

### 2.1 Question trace

Fill the last two columns for every query before drawing entities.

| Q           | Query (short)                                                                                   | Entities touched | Attributes needed |
| ----------- | ----------------------------------------------------------------------------------------------- | ---------------- | ----------------- |
| M2-Q1 (i)   | Each guest's ID, name, total completed stays; most frequent first                               |                  |                   |
| M2-Q2 (ii)  | Names and ages of all guests assigned to a given reservation and room                           |                  |                   |
| M2-Q3 (iii) | Room assignments where occupants reach the room's maximum occupancy                             |                  |                   |
| M2-Q4 (iv)  | Reservations with at least one child and at least one adult                                     |                  |                   |
| M2-Q5 (v)   | Adults/children/infants per hotel on a date, with hotel subtotals and grand total (ROLLUP/CUBE) |                  |                   |
| M2-Q6 (vi)  | Accessible rooms assigned to guests in a date range                                             |                  |                   |
| M2-Q7 (vii) | Extra query: guests or occupancy                                                                |                  |                   |

### 2.2 Entities and attributes

<!-- Copy the entity card format from Section 0 for each entity. One card per entity. -->

#### Entity: <Name>

*<what a row represents>*

| Attribute | Suggested type | Key | Needed by | Notes |
| --------- | -------------- | --- | --------- | ----- |
|           |                |     |           |       |

### 2.3 Relationships

| Entity A | Relationship (verb) | Entity B | Cardinality | Optionality | Needed by |
| -------- | ------------------- | -------- | ----------- | ----------- | --------- |
|          |                     |          |             |             |           |

### 2.4 Assumptions and open questions

| #   | Type                  | Item | Raised for (Q or entity) | Resolution |
| --- | --------------------- | ---- | ------------------------ | ---------- |
| 2.1 | Assumption / Question |      |                          |            |

### 2.5 Ideas parking lot (optional)

Free-form. Anything that is not an entity or attribute: potential constraints, procedures, triggers, optimisation ideas, query tricks, denormalisation thoughts. Tag each so it can be picked up later.

* `[constraint]`
* `[procedure]`
* `[trigger]`
* `[optimisation]`
* `[other]`

<!-- SECTION 2 END -->

***

<!-- SECTION 3 START: MEMBER 3 -->

## Section 3: Member 3 - Room rates and optional services

**Status:** `Not started`

### 3.1 Question trace

Fill the last two columns for every query before drawing entities.

| Q           | Query (short)                                                                                           | Entities touched | Attributes needed |
| ----------- | ------------------------------------------------------------------------------------------------------- | ---------------- | ----------------- |
| M3-Q1 (i)   | Regular nightly rate and rate after 10% promo for Deluxe rooms at a hotel (labelled calculated columns) |                  |                   |
| M3-Q2 (ii)  | Optional services on a reservation: quantity, charging basis, calculated charge                         |                  |                   |
| M3-Q3 (iii) | Optional services that have never been purchased                                                        |                  |                   |
| M3-Q4 (iv)  | Total value of breakfast packages sold per hotel in a month                                             |                  |                   |
| M3-Q5 (v)   | Optional-service revenue by hotel and service type, with subtotals and grand total (ROLLUP/CUBE)        |                  |                   |
| M3-Q6 (vi)  | Top five reservations by optional-service charges in a date range                                       |                  |                   |
| M3-Q7 (vii) | Extra query: rates or services                                                                          |                  |                   |

### 3.2 Entities and attributes

<!-- Copy the entity card format from Section 0 for each entity. One card per entity. -->

#### Entity: <Name>

*<what a row represents>*

| Attribute | Suggested type | Key | Needed by | Notes |
| --------- | -------------- | --- | --------- | ----- |
|           |                |     |           |       |

### 3.3 Relationships

| Entity A | Relationship (verb) | Entity B | Cardinality | Optionality | Needed by |
| -------- | ------------------- | -------- | ----------- | ----------- | --------- |
|          |                     |          |             |             |           |

### 3.4 Assumptions and open questions

| #   | Type                  | Item | Raised for (Q or entity) | Resolution |
| --- | --------------------- | ---- | ------------------------ | ---------- |
| 3.1 | Assumption / Question |      |                          |            |

### 3.5 Ideas parking lot (optional)

Free-form. Anything that is not an entity or attribute: potential constraints, procedures, triggers, optimisation ideas, query tricks, denormalisation thoughts. Tag each so it can be picked up later.

* `[constraint]`
* `[procedure]`
* `[trigger]`
* `[optimisation]`
* `[other]`

<!-- SECTION 3 END -->

***

<!-- SECTION 4 START: MEMBER 4 -->

## Section 4: Member 4 - Changes, cancellations and no-shows

**Status:** `Not started`

### 4.1 Question trace

Fill the last two columns for every query before drawing entities.

| Q           | Query (short)                                                                                                               | Entities touched | Attributes needed |
| ----------- | --------------------------------------------------------------------------------------------------------------------------- | ---------------- | ----------------- |
| M4-Q1 (i)   | Reservations cancelled < 48h before check-in, with the applicable cancellation charge                                       |                  |                   |
| M4-Q2 (ii)  | Original and revised stay dates for reservations changed in a month                                                         |                  |                   |
| M4-Q3 (iii) | Guests who have cancelled more than one reservation                                                                         |                  |                   |
| M4-Q4 (iv)  | No-shows per hotel in a date range, highest to lowest                                                                       |                  |                   |
| M4-Q5 (v)   | Cancellation counts by hotel and month for a year, with subtotals and grand total (ROLLUP/CUBE)                             |                  |                   |
| M4-Q6 (vi)  | Hotel with highest cancellation rate in a year (state the formula). Note: brief's numbering reads 'vi. xxvii.', treat as Q6 |                  |                   |
| M4-Q7 (vii) | Extra query: changes, cancellations or no-shows                                                                             |                  |                   |

### 4.2 Entities and attributes

<!-- Copy the entity card format from Section 0 for each entity. One card per entity. -->

#### Entity: <Name>

*<what a row represents>*

| Attribute | Suggested type | Key | Needed by | Notes |
| --------- | -------------- | --- | --------- | ----- |
|           |                |     |           |       |

### 4.3 Relationships

| Entity A | Relationship (verb) | Entity B | Cardinality | Optionality | Needed by |
| -------- | ------------------- | -------- | ----------- | ----------- | --------- |
| f        |                     |          |             |             |           |

### 4.4 Assumptions and open questions

| #   | Type                  | Item | Raised for (Q or entity) | Resolution |
| --- | --------------------- | ---- | ------------------------ | ---------- |
| 4.1 | Assumption / Question |      |                          |            |

### 4.5 Ideas parking lot (optional)

Free-form. Anything that is not an entity or attribute: potential constraints, procedures, triggers, optimisation ideas, query tricks, denormalisation thoughts. Tag each so it can be picked up later.

* `[constraint]`
* `[procedure]`
* `[trigger]`
* `[optimisation]`
* `[other]`

<!-- SECTION 4 END -->

***

<!-- SECTION 5 START: MEMBER 5 -->

## Section 5: Member 5 - Billing, payments and maintenance

**Status:** `In Progress`

### 5.1 Question trace

Fill the last two columns for every query before drawing entities.

| Q           | Query (short)                                                                                       | Entities touched                                         | Attributes needed                                                                                                                                                                                                                                                                                                            |
| ----------- | --------------------------------------------------------------------------------------------------- | -------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| M5-Q1 (i)   | Room charges, service charges, discounts, payments and outstanding balance for a reservation        | Reservation, RoomAssignment, ReservationService, Payment | Reservation.ReservationID, Reservation.DiscountAmount, RoomAssignment.ReservationID, RoomAssignment.RoomChargeAmount, ReservationService.ReservationID, ReservationService.ServiceChargeAmount, Payment.ReservationID, Payment.PaymentAmount, Payment.PaymentType. Calculated: OutstandingBalance                            |
| M5-Q2 (ii)  | Checked-out reservations that still have an outstanding balance                                     | Reservation, RoomAssignment, ReservationService, Payment | Reservation.ReservationID, Reservation.Status, Reservation.DiscountAmount, RoomAssignment.ReservationID, RoomAssignment.RoomChargeAmount, ReservationService.ReservationID, ReservationService.ServiceChargeAmount, Payment.ReservationID, Payment.PaymentAmount,  Payment.PaymentType, Calculated: OutstandingBalance (> 0) |
| M5-Q3 (iii) | Rooms currently under maintenance: hotel name, room number, start date, expected completion         | Maintenance, Room, Hotel                                 | Maintenance.RoomID, Maintenance.StartDate, Maintenance.ExpectedCompletionDate, Room.RoomID, Room.HotelID, Room.RoomNumber, Hotel.HotelID, Hotel.HotelName                                                                                                                                                                    |
| M5-Q4 (iv)  | Rooms with no completed stays in a date range                                                       | Room, RoomAssignment, Reservation                        | Room.RoomID, RoomAssignment.RoomID, RoomAssignment.ReservationID, Reservation.ReservationID, Reservation.Status, Reservation.CheckInDate, Reservation.CheckOutDate                                                                                                                                                           |
| M5-Q5 (v)   | Total payments by hotel and payment method in a month, with subtotals and grand total (ROLLUP/CUBE) | Payment, Reservation, Hotel                              | Payment.ReservationID, Payment.PaymentAmount, Payment.PaymentDate, Payment.PaymentMethod, Payment.PaymentType, Reservation.ReservationID, Reservation.HotelID, Hotel.HotelID, Hotel.HotelName                                                                                                                                |
| M5-Q6 (vi)  | Hotel with highest revenue from completed stays in a quarter                                        | Hotel, Reservation, RoomAssignment, ReservationService   | Hotel.HotelID, Hotel.HotelName, Reservation.ReservationID, Reservation.HotelID, Reservation.Status, Reservation.CheckOutDate, Reservation.DiscountAmount, RoomAssignment.ReservationID, RoomAssignment.RoomChargeAmount, ReservationService.ReservationID, ReservationService.ServiceChargeAmount. Calculated: Revenue       |
| M5-Q7 (vii) | Extra query: billing, payments or maintenance                                                       | Maintenance, Room, Hotel                                 | Maintenance.MaintenanceID, Maintenance.RoomID, Maintenance.StartDate, Maintenance.ExpectedCompletionDate, Room.RoomID, Room.HotelID, Hotel.HotelID, Hotel.HotelName. Calculated: downtime days                                                                                                                               |

### 5.2 Entities and attributes

<!-- One table per entity. -->

#### Entity: Hotel

*<what a row represents>*

| Attribute | Suggested type | Key | Needed by | Notes |
| --------- | -------------- | --- | --------- | ----- |
| HotelID   | INT            | PK  |           |       |
| HotelName | VARCHAR(100)   |     |           |       |

#### Entity: Room

*<what a row represents>*

| Attribute  | Suggested type | Key                 | Needed by | Notes |
| ---------- | -------------- | ------------------- | --------- | ----- |
| RoomID     | INT            | PK                  |           |       |
| HotelID    | INT            | FK -> Hotel.HotelID |           |       |
| RoomNumber | VARCHAR(10)    |                     |           |       |

#### Entity: Reservation

*<what a row represents>*

| Attribute      | Suggested type | Key                               | Needed by | Notes                                                  |
| -------------- | -------------- | --------------------------------- | --------- | ------------------------------------------------------ |
| ReservationID  | INT            | PK                                |           |                                                        |
| HotelID        | INT            | FK -> Hotel.HotelID               |           |                                                        |
| Status         | VARCHAR(20)    | FK -> RoomCategory.RoomCategoryID |           | Pending, Confirmed, Checked In, Checked Out, Cancelled |
| CheckInDate    | DATE           |                                   |           |                                                        |
| CheckOutDate   | DATE           |                                   |           |                                                        |
| DiscountAmount | DECIMAL(10,2)  |                                   |           |                                                        |

#### Entity: RoomAssignment

A room assigned for a reservation

| Attribute        | Suggested type | Key                             | Needed by | Notes |
| ---------------- | -------------- | ------------------------------- | --------- | ----- |
| RoomAssignmentID | INT            | PK                              |           |       |
| ReservationID    | INT            | FK -> Reservation.ReservationID |           |       |
| RoomID           | INT            | FK -> Room.RoomID               |           |       |
| RoomCharge       | DECIMAL(10,2)  |                                 |           |       |

#### Entity: ReservationService

*a bridge between Reservation and OptionalService (M:M relationship)*

| Attribute           | Suggested type | Key                                         | Needed by | Notes |
| ------------------- | -------------- | ------------------------------------------- | --------- | ----- |
| ReservationID       | INT            | PK, FK -> Reservation.ReservationID         |           |       |
| OptionalServiceID   | INT            | PK, FK -> OptionalService.OptionalServiceID |           |       |
| ServiceChargeAmount | DECIMAL(12,2)  |                                             |           |       |

#### Entity: Payment

*<what a row represents>*

| Attribute     | Suggested type | Key                             | Needed by | Notes                                          |
| ------------- | -------------- | ------------------------------- | --------- | ---------------------------------------------- |
| PaymentID     | INT            | PK                              |           |                                                |
| ReservationID | INT            | FK -> Reservation.ReservationID |           |                                                |
| PaymentDate   | DATE           |                                 |           |                                                |
| PaymentAmount | DECIMAL(12,2)  |                                 |           | > 0                                            |
| PaymentMethod | VARCHAR(20)    |                                 |           | Cash, Credit Card, Debit Card, Online Transfer |
| PaymentType   | VARCHAR(20)    |                                 |           | Payment, Refund                                |

#### Entity: Maintenance

*<what a row represents>*

| Attribute              | Suggested type | Key               | Needed by | Notes |
| ---------------------- | -------------- | ----------------- | --------- | ----- |
| MaintenanceID          | INT            | PK                |           |       |
| RoomID                 | INT            | FK -> Room.RoomID |           |       |
| StartDate              | DATE           |                   |           |       |
| ExpectedCompletionDate | DATE           |                   |           | > 0   |

### 5.3 Relationships

| Entity A    | Relationship (verb) | Entity B                                 | Cardinality                          | Optionality                                                              | Needed by |
| ----------- | ------------------- | ---------------------------------------- | ------------------------------------ | ------------------------------------------------------------------------ | --------- |
| Hotel       | owns                | Room                                     | 1 : N                                | Room belongs to exactly 1 Hotel                                          |           |
| Hotel       | receives            | Reservation                              | 1 : N                                | Reservation belongs to exactly 1 Hotel                                   |           |
| Reservation | is allocated        | RoomAssignment                           | 1 : N                                | Assignment belongs to 1 Reservation                                      |           |
| Room        | is used in          | RoomAssignment                           | 1 : N                                | Room may have 0 assignments                                              |           |
| Reservation | includes            | OptionalService (via ReservationService) | M:N (solved with ReservationService) | Reservation may have 0 services. OptionalService may have 0 reservations |           |
| Reservation | is settled by       | Payment                                  | 1 : N                                | Reservation may have 0 payments                                          |           |
| Room        | undergoes           | Maintenance                              | 1 : N                                | Room may have 0 maintenance periods                                      |           |

### 5.4 Assumptions and open questions

| #   | Type       | Item                                                                                                                                                                                                                                                              | Raised for | Resolution       |
| --- | ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- | ---------------- |
| 5.1 | Assumption | "Completed stay" means `Status = 'Checked Out'`.                                                                                                                                                                                                                  | Q4, Q6     |                  |
| 5.2 | Assumption | Revenue for Q6 is room charge + service charge − discount, attributed to the quarter of `CheckOutDate`.                                                                                                                                                           | Q6         |                  |
| 5.3 | Assumption | Q5 counts only `PaymentType = 'Payment'`, with refunds shown separately or netted.                                                                                                                                                                                | Q5         |                  |
| 5.4 | Assumption | Q7 is "maintenance downtime per hotel": rooms affected, total days and average duration, using the start and expected completion dates.                                                                                                                           | Q7         |                  |
| 5.5 | Question   | `RoomChargeAmount` and `ServiceChargeAmount` are stored charges, calculated by whoever creates the bill. Members 1 and 3 may hold the rate data (RoomRate, service price). If the team prefers to derive these, M5 would also need `RoomRate` and `RoomCategory`. | Q1, Q2, Q6 | Decide at merge  |
| 5.6 | Question   | One reservation-level `DiscountAmount` is assumed. Member 3's promo may need its own entity.                                                                                                                                                                      | Q1, Q6     | Revisit at merge |
| 5.7 | Question   | "Currently under maintenance" is taken as `StartDate <= today <= ExpectedCompletionDate`. A room finished early would still show. Is that acceptable, or do we need an actual completion date or a status?                                                        | Q3         | Decide at merge  |

### 5.5 Ideas parking lot (optional)

Free-form. Anything that is not an entity or attribute: potential constraints, procedures, triggers, optimisation ideas, query tricks, denormalisation thoughts. Tag each so it can be picked up later.

- [constraint]: `CHECK (PaymentAmount > 0)`, `CHECK (PaymentType IN ('Payment','Refund'))`, and `CHECK (ExpectedCompletionDate >= StartDate)`.
- [procedure]: `sp_GetReservationBill(@ReservationID)`, returning the itemised bill and balance.
- [trigger]: Block a Payment that would overpay the balance, or a refund larger than the payments received.
- [optimisation]:
- [other]:
