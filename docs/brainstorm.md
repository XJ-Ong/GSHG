# GSHG Brainstorm: Minimum Entities and Attributes

> **Purpose.** Stage 1 of the design. Each member lists the _minimum_ entities and attributes needed to answer their own 7 queries. Merging the five sections and covering the rest of the case study will be done later in a separate file. Normalisation (3NF) and the final ERD come **after** this file, not in it.

> **Out of scope here:** constraints, stored procedures, triggers and optimisation. Do not design them in this file. If an idea occurs to you, drop it in the _Ideas parking lot_ of your section so it is not lost.

---

## 0. How to use this file

### Ground rules

1. **Edit only your own section** (Section 1 to 5 for Members 1 to 5). Sections are fenced by `<!-- SECTION n START/END -->` comments; keep your edits between them. This keeps Git merges conflict-free.
2. **Minimum means minimum.** Add an attribute only if a query needs it. Every attribute must have a **Needed by** reference such as `M3-Q2`. Attributes added on a hunch go in the Ideas parking lot, not in an entity table.
3. **Overlap between members is expected.** `Reservation`, `Room`, `Hotel` will appear in several sections. Use the same entity name as other members where the concept is the same, and list only the attributes _your_ queries need. Do not try to deduplicate; that happens in the later merge.
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
| `D`                 | **Derived**: can be calculated from other attributes (for example total nights). Listed so we know the query needs it, but it will probably _not_ be stored (3NF). |
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

#### Entity: Guest _(example)_

_A person who makes or stays in a reservation._

| Attribute | Suggested type | Key | Needed by | Notes                                 |
| --------- | -------------- | --- | --------- | ------------------------------------- |
| GuestID   | INT            | PK  | M1-Q2     |                                       |
| FullName  | VARCHAR(100)   | -   | M1-Q2     | split into first/last later if needed |

## Recommended starting entities (from the case study only)

**Recommended Entities from Case Study:**

- Hotel
- RoomCategory
- Room
- RoomRate
- Guest
- Reservation
- RoomAssignment
- OptionalService
- ReservationService
- Payment
- Maintenance

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

_<what a row represents>_

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

- `[constraint]`
- `[procedure]`
- `[trigger]`
- `[optimisation]`
- `[other]`

<!-- SECTION 1 END -->

---

<!-- SECTION 2 START: MEMBER 2 -->

## Section 2: Member 2 - Guests and room occupancy

**Status:** `Done`

### 2.1 Question trace

Fill the last two columns for every query before drawing entities.

| Q           | Query (short)                                                                                     | Entities touched                                                            | Attributes needed                                                                                                                                                                                                                                                                                                                                                   |
| ----------- | ------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| M2-Q1 (i)   | Each guest's ID, name, total completed stays; most frequent first                                 | Guest, RoomOccupant, RoomAssignment, Reservation                            | Guest.GuestID, Guest.FirstName, Guest.LastName, Reservation.ReservationID, Reservation.Status, RoomAssignment.RoomAssignmentID, RoomOccupant.GuestID. Calculated: TotalCompletedStays (`COUNT(DISTINCT Reservation.ReservationID)` where `Status = 'Checked Out'`)                                                                                                  |
| M2-Q2 (ii)  | Names and ages of all guests assigned to a given reservation and room                             | Reservation, RoomAssignment, Room, RoomOccupant, Guest                      | Reservation.ReservationID, Reservation.CheckInDate, Room.RoomID, Room.RoomNumber, RoomAssignment.RoomAssignmentID, RoomAssignment.ReservationID, RoomAssignment.RoomID, RoomOccupant.RoomAssignmentID, RoomOccupant.GuestID, Guest.GuestID, Guest.FirstName, Guest.LastName, Guest.DateOfBirth. Calculated: Age (derived from `DateOfBirth` and `CheckInDate`)      |
| M2-Q3 (iii) | Room assignments where occupants reach the room's maximum occupancy                               | RoomAssignment, Room, RoomCategory, RoomOccupant                            | RoomAssignment.RoomAssignmentID, RoomAssignment.RoomID, Room.RoomID, Room.RoomCategoryID, RoomCategory.RoomCategoryID, RoomCategory.CategoryName, RoomCategory.MaxOccupancy, RoomOccupant.RoomAssignmentID, RoomOccupant.GuestID. Calculated: OccupantCount (`COUNT(RoomOccupant.GuestID) = RoomCategory.MaxOccupancy`)                                             |
| M2-Q4 (iv)  | Reservations with at least one child and at least one adult                                       | Reservation, RoomAssignment, RoomOccupant, Guest                            | Reservation.ReservationID, Reservation.CheckInDate, RoomAssignment.ReservationID, RoomAssignment.RoomAssignmentID, RoomOccupant.RoomAssignmentID, RoomOccupant.GuestID, Guest.GuestID, Guest.DateOfBirth. Calculated: Age, Adult flag (`Age >= 18`), Child flag (`Age >= 2 AND Age < 18`)                                                                           |
| M2-Q5 (v)   | Adults/children/infants per hotel on a date, with hotel subtotals and grand total (ROLLUP/CUBE)   | Hotel, Reservation, RoomAssignment, RoomOccupant, Guest                     | Hotel.HotelID, Hotel.HotelName, Reservation.ReservationID, Reservation.HotelID, Reservation.CheckInDate, Reservation.CheckOutDate, Reservation.Status, RoomAssignment.RoomAssignmentID, RoomOccupant.RoomAssignmentID, RoomOccupant.GuestID, Guest.GuestID, Guest.DateOfBirth. Calculated: AgeCategory (Adult: >=18, Child: 2-17, Infant: <2), OccupantCount        |
| M2-Q6 (vi)  | Accessible rooms assigned to guests in a date range                                               | Room, Hotel, RoomAssignment, Reservation, RoomOccupant, Guest               | Room.RoomID, Room.HotelID, Room.RoomNumber, Room.IsAccessible (`= 1`), Hotel.HotelID, Hotel.HotelName, Reservation.ReservationID, Reservation.CheckInDate, Reservation.CheckOutDate, Reservation.Status, RoomAssignment.RoomAssignmentID, RoomOccupant.RoomAssignmentID, RoomOccupant.GuestID, Guest.GuestID, Guest.FirstName, Guest.LastName                       |
| M2-Q7 (vii) | Extra query: monthly room occupancy rate (%) and distinct guest count per hotel and room category | Hotel, RoomCategory, Room, RoomAssignment, Reservation, RoomOccupant, Guest | Hotel.HotelID, Hotel.HotelName, RoomCategory.RoomCategoryID, RoomCategory.CategoryName, Room.RoomID, RoomAssignment.RoomAssignmentID, Reservation.ReservationID, Reservation.CheckInDate, Reservation.CheckOutDate, Reservation.Status, RoomOccupant.GuestID. Calculated: OccupiedRoomNights, TotalAvailableRoomNights, OccupancyRatePercentage, DistinctGuestCount |

### 2.2 Entities and attributes

<!-- Copy the entity card format from Section 0 for each entity. One card per entity. -->

#### Entity: Guest

_A person who stays in a room or makes a reservation._

| Attribute   | Suggested type | Key | Needed by           | Notes                                                            |
| ----------- | -------------- | --- | ------------------- | ---------------------------------------------------------------- |
| GuestID     | INT            | PK  | M2-Q1, M2-Q2        | Unique identifier for each individual guest                      |
| FirstName   | VARCHAR(50)    | -   | M2-Q1, M2-Q2, M2-Q6 | Guest given name                                                 |
| LastName    | VARCHAR(50)    | -   | M2-Q1, M2-Q2, M2-Q6 | Guest family name                                                |
| DateOfBirth | DATE           | -   | M2-Q2, M2-Q4, M2-Q5 | Used to derive age and age classification (adult, child, infant) |
| Email       | VARCHAR(100)   | UK  | M2-Q1               | Guest contact / unique profile reference                         |
| PhoneNumber | VARCHAR(20)    | -   | M2-Q1               | Contact phone number                                             |
| Age         | INT            | D   | M2-Q2, M2-Q4, M2-Q5 | Derived: `DATEDIFF(YEAR, DateOfBirth, CheckInDate)` (not stored) |

#### Entity: RoomOccupant

_A bridge entity recording each individual guest staying in an assigned room during a reservation._

| Attribute         | Suggested type | Key                                       | Needed by                                | Notes                                                     |
| ----------------- | -------------- | ----------------------------------------- | ---------------------------------------- | --------------------------------------------------------- |
| RoomAssignmentID  | INT            | PK, FK -> RoomAssignment.RoomAssignmentID | M2-Q1, M2-Q2, M2-Q3, M2-Q4, M2-Q5, M2-Q6 | Composite PK component; links to assigned room            |
| GuestID           | INT            | PK, FK -> Guest.GuestID                   | M2-Q1, M2-Q2, M2-Q3, M2-Q4, M2-Q5, M2-Q6 | Composite PK component; links to staying guest            |
| IsPrimaryOccupant | BIT            | -                                         | M2-Q2                                    | 1 if this guest is the lead occupant for the room, else 0 |

#### Entity: RoomAssignment

_The allocation of a specific physical room to a reservation for a stay._

| Attribute              | Suggested type | Key                             | Needed by                                       | Notes                             |
| ---------------------- | -------------- | ------------------------------- | ----------------------------------------------- | --------------------------------- |
| RoomAssignmentID       | INT            | PK                              | M2-Q1, M2-Q2, M2-Q3, M2-Q4, M2-Q5, M2-Q6, M2-Q7 | Unique assignment record          |
| ReservationID          | INT            | FK -> Reservation.ReservationID | M2-Q1, M2-Q2, M2-Q3, M2-Q4, M2-Q5, M2-Q6, M2-Q7 | Links assignment to booking       |
| RoomID                 | INT            | FK -> Room.RoomID               | M2-Q2, M2-Q3, M2-Q6, M2-Q7                      | Links assignment to physical room |
| ActualCheckInDateTime  | DATETIME       | -                               | M2-Q1, M2-Q5                                    | Recorded actual check-in time     |
| ActualCheckOutDateTime | DATETIME       | -                               | M2-Q1                                           | Recorded actual check-out time    |

#### Entity: Room

_An individual physical room within a hotel._

| Attribute      | Suggested type | Key                               | Needed by                  | Notes                                                 |
| -------------- | -------------- | --------------------------------- | -------------------------- | ----------------------------------------------------- |
| RoomID         | INT            | PK                                | M2-Q2, M2-Q3, M2-Q6, M2-Q7 | Unique room identifier                                |
| HotelID        | INT            | FK -> Hotel.HotelID               | M2-Q5, M2-Q6, M2-Q7        | Hotel where the room is located                       |
| RoomCategoryID | INT            | FK -> RoomCategory.RoomCategoryID | M2-Q3, M2-Q7               | Category type classification                          |
| RoomNumber     | VARCHAR(10)    | -                                 | M2-Q2, M2-Q6               | Room door number (unique within a hotel)              |
| IsAccessible   | BIT            | -                                 | M2-Q6                      | 1 if wheelchair/disabled accessible room, 0 otherwise |

#### Entity: RoomCategory

_A classification or tier of hotel rooms._

| Attribute      | Suggested type | Key | Needed by    | Notes                                                               |
| -------------- | -------------- | --- | ------------ | ------------------------------------------------------------------- |
| RoomCategoryID | INT            | PK  | M2-Q3, M2-Q7 | Unique room category identifier                                     |
| CategoryName   | VARCHAR(50)    | -   | M2-Q3, M2-Q7 | Standard, Deluxe, Family, Suite                                     |
| MaxOccupancy   | INT            | -   | M2-Q3        | Maximum occupants allowed in rooms of this category (e.g., 2, 3, 5) |

#### Entity: Reservation

_A guest booking for one or more rooms across stay dates._

| Attribute     | Suggested type | Key                 | Needed by                                | Notes                                                  |
| ------------- | -------------- | ------------------- | ---------------------------------------- | ------------------------------------------------------ |
| ReservationID | INT            | PK                  | M2-Q1, M2-Q2, M2-Q4, M2-Q5, M2-Q6, M2-Q7 | Unique reservation identifier                          |
| HotelID       | INT            | FK -> Hotel.HotelID | M2-Q5, M2-Q7                             | Target hotel for the reservation                       |
| GuestID       | INT            | FK -> Guest.GuestID | M2-Q1                                    | Primary booking contact guest                          |
| CheckInDate   | DATE           | -                   | M2-Q2, M2-Q4, M2-Q5, M2-Q6, M2-Q7        | Scheduled check-in date                                |
| CheckOutDate  | DATE           | -                   | M2-Q5, M2-Q6, M2-Q7                      | Scheduled check-out date                               |
| Status        | VARCHAR(20)    | -                   | M2-Q1, M2-Q5, M2-Q6, M2-Q7               | Pending, Confirmed, Checked In, Checked Out, Cancelled |

#### Entity: Hotel

_A hotel property within the GSHG group._

| Attribute | Suggested type | Key | Needed by           | Notes                      |
| --------- | -------------- | --- | ------------------- | -------------------------- |
| HotelID   | INT            | PK  | M2-Q5, M2-Q6, M2-Q7 | Unique hotel identifier    |
| HotelName | VARCHAR(100)   | -   | M2-Q5, M2-Q6, M2-Q7 | Name of the hotel property |
| Country   | VARCHAR(50)    | -   | M2-Q5               | Country location           |

### 2.3 Relationships

| Entity A       | Relationship (verb) | Entity B       | Cardinality | Optionality                                                                | Needed by                                |
| -------------- | ------------------- | -------------- | ----------- | -------------------------------------------------------------------------- | ---------------------------------------- |
| Hotel          | operates            | Room           | 1 : N       | Hotel must have >= 1 Room; Room belongs to exactly 1 Hotel                 | M2-Q5, M2-Q6, M2-Q7                      |
| RoomCategory   | classifies          | Room           | 1 : N       | Category has >= 0 Rooms; Room must belong to exactly 1 Category            | M2-Q3, M2-Q7                             |
| Hotel          | receives            | Reservation    | 1 : N       | Hotel has >= 0 Reservations; Reservation belongs to exactly 1 Hotel        | M2-Q5, M2-Q7                             |
| Guest          | books               | Reservation    | 1 : N       | Guest can book >= 0 Reservations; Reservation has 1 primary Guest          | M2-Q1                                    |
| Reservation    | contains            | RoomAssignment | 1 : N       | Reservation must have >= 1 Assignment; Assignment belongs to 1 Reservation | M2-Q1, M2-Q2, M2-Q3, M2-Q4, M2-Q5, M2-Q7 |
| Room           | is allocated in     | RoomAssignment | 1 : N       | Room can have >= 0 Assignments; Assignment references 1 Room               | M2-Q2, M2-Q3, M2-Q6, M2-Q7               |
| RoomAssignment | accommodates        | Guest          | M : N       | Solved by RoomOccupant bridge entity (1 Assignment has 1..N Occupants)     | M2-Q1, M2-Q2, M2-Q3, M2-Q4, M2-Q5, M2-Q6 |
| RoomAssignment | has                 | RoomOccupant   | 1 : N       | Assignment has >= 1 Occupant; Occupant belongs to 1 Assignment             | M2-Q1, M2-Q2, M2-Q3, M2-Q4, M2-Q5, M2-Q6 |
| Guest          | stays as            | RoomOccupant   | 1 : N       | Guest can stay in >= 0 RoomOccupant records; Occupant refers to 1 Guest    | M2-Q1, M2-Q2, M2-Q3, M2-Q4, M2-Q5, M2-Q6 |

### 2.4 Assumptions and open questions

| #   | Type       | Item                                                                                                                                                                                                        | Raised for (Q or entity)  | Resolution                                                                             |
| --- | ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------- | -------------------------------------------------------------------------------------- |
| 2.1 | Assumption | Guest age is derived from `DateOfBirth` and `Reservation.CheckInDate` (or stay date), adhering to 3NF rather than storing a mutable `Age` column.                                                           | M2-Q2, M2-Q4, M2-Q5       | Accepted; age dynamically calculated in SQL queries / views                            |
| 2.2 | Assumption | Age band categories are based on case study rules R9 & R10: Adult (Age >= 18), Child (Age >= 2 and Age < 18), Infant (Age < 2).                                                                             | M2-Q4, M2-Q5              | Handled via CASE expression on derived age                                             |
| 2.3 | Assumption | A "completed stay" (M2-Q1) corresponds to `Reservation.Status = 'Checked Out'` (or `ActualCheckOutDateTime IS NOT NULL`). A stay counts for all registered occupants in `RoomOccupant`.                     | M2-Q1                     | Count distinct completed reservations per guest                                        |
| 2.4 | Assumption | `MaxOccupancy` is primarily an attribute of `RoomCategory` (e.g. Standard: 2, Deluxe: 3, Family: 5), but physical rooms can inherit/adhere to their category limit.                                         | M2-Q3                     | Defined on RoomCategory; could optionally be placed on Room if individual rooms differ |
| 2.5 | Assumption | Stays active on a given date (M2-Q5) are identified where `CheckInDate <= @TargetDate AND CheckOutDate > @TargetDate` and `Status IN ('Checked In', 'Confirmed')`.                                          | M2-Q5                     | Accepted standard hotel night stay logic                                               |
| 2.6 | Assumption | For M2-Q7, the chosen extra query is: "Monthly room occupancy rate (%) and distinct guest count per hotel and room category for a specified month" (Occupied room-nights divided by available room-nights). | M2-Q7                     | Selected as standard high-value hospitality metric                                     |
| 2.7 | Question   | Does `Reservation` also have a primary booker `GuestID` in addition to room occupants?                                                                                                                      | Reservation, RoomOccupant | Yes, booking contact guest on Reservation; all physical stayers in RoomOccupant        |

### 2.5 Ideas parking lot (optional)

Free-form. Anything that is not an entity or attribute: potential constraints, procedures, triggers, optimisation ideas, query tricks, denormalisation thoughts. Tag each so it can be picked up later.

- `[constraint]` `CHECK (DateOfBirth <= GETDATE())` on `Guest` to prevent invalid future birthdates.
- `[constraint]` `CHECK (MaxOccupancy > 0)` on `RoomCategory`.
- `[constraint]` `CHECK (IsAccessible IN (0, 1))` on `Room`.
- `[procedure]` `sp_AssignGuestToRoom(@RoomAssignmentID, @GuestID, @IsPrimaryOccupant)`: assigns a guest to an allocated room while validating against room maximum occupancy and checking age rules.
- `[procedure]` `sp_GetHotelOccupancySummary(@HotelID, @Month, @Year)`: outputs room nights occupied, total available room nights, guest counts, and occupancy percentage.
- `[trigger]` Enforce Maximum Occupancy: trigger on `RoomOccupant` that aborts insertion if `COUNT(RoomOccupant)` exceeds `RoomCategory.MaxOccupancy` for that room assignment.
- `[trigger]` Enforce Adult Stay Rule (Case Study R9): trigger validating that each room assignment has at least one adult (derived `Age >= 18`) before check-in.
- `[optimisation]` Filtered/Nonclustered index on `Room(HotelID, IsAccessible) INCLUDE (RoomNumber)` to accelerate accessible room queries (M2-Q6).
- `[optimisation]` Composite index on `Reservation(HotelID, CheckInDate, CheckOutDate, Status)` to optimize date range occupancy lookups (M2-Q5, M2-Q7).
- `[optimisation]` Nonclustered index on `Guest(DateOfBirth)` to accelerate age-band derivations and aggregations.
- `[other]` Consider a database view `vw_CurrentRoomOccupancy` combining RoomAssignment, Room, and RoomOccupant with derived age categories for simplified reporting.

<!-- SECTION 2 END -->

---

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

_<what a row represents>_

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

- `[constraint]`
- `[procedure]`
- `[trigger]`
- `[optimisation]`
- `[other]`

<!-- SECTION 3 END -->

---

<!-- SECTION 4 START: MEMBER 4 -->

## Section 4: Member 4 - Changes, cancellations and no-shows

**Status:** `Forced to start by leader😭😭😭`

### 4.1 Question trace

Fill the last two columns for every query before drawing entities.

| Q           | Query (short)                                                                                                               | Entities touched                                          | Attributes needed                                                                                                                                                                                                                                                                                                                                                                 |
| ----------- | --------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| M4-Q1 (i)   | Reservations cancelled < 48h before check-in, with the applicable cancellation charge                                       | Reservation, Cancellation, ReservationRoom                | Reservation.ReservationID, Reservation.CheckInDate, Reservation.CheckInDeadline, Cancellation.CancellationDate, Cancellation.CancellationCharge, ReservationRoom.RoomCharge                                                                                                                                                                                                       |
| M4-Q2 (ii)  | Original and revised stay dates for reservations changed in a month                                                         | Reservation, ReservationChange                            | Reservation.ReservationID, ReservationChange.ChangeDate, ReservationChange.OriginalCheckInDate, ReservationChange.OriginalCheckOutDate, ReservationChange.NewCheckInDate, ReservationChange.NewCheckOutDate                                                                                                                                                                       |
| M4-Q3 (iii) | Guests who have cancelled more than one reservation                                                                         | Guest, Reservation, Cancellation                          | Guest.GuestID, Guest.FirstName, Guest.LastName, Reservation.ReservationID, Reservation.GuestID, Cancellation.ReservationID, Cancellation.CancellationDate                                                                                                                                                                                                                         |
| M4-Q4 (iv)  | No-shows per hotel in a date range, highest to lowest                                                                       | Hotel, Room, ReservationRoom, Reservation                 | Hotel.HotelID, Hotel.HotelName, Room.HotelID, ReservationRoom.RoomID, ReservationRoom.ReservationID, Reservation.ReservationID, Reservation.ReservationStatus, Reservation.CheckInDate, Reservation.CheckInDeadline                                                                                                                                                               |
| M4-Q5 (v)   | Cancellation counts by hotel and month for a year, with subtotals and grand total (ROLLUP/CUBE)                             | Hotel, Room, ReservationRoom, Reservation, Cancellation   | Hotel.HotelID, Hotel.HotelName, Room.HotelID, ReservationRoom.RoomID, ReservationRoom.ReservationID, Reservation.ReservationID, Cancellation.ReservationID, Cancellation.CancellationDate                                                                                                                                                                                         |
| M4-Q6 (vi)  | Hotel with highest cancellation rate in a year (state the formula). Note: brief's numbering reads 'vi. xxvii.', treat as Q6 | Hotel, Room, ReservationRoom, Reservation, Cancellation   | Hotel.HotelID, Hotel.HotelName, Room.HotelID, ReservationRoom.RoomID, ReservationRoom.ReservationID, Reservation.ReservationID, Reservation.CheckInDate, Cancellation.ReservationID, Cancellation.CancellationDate                                                                                                                                                                |
| M4-Q7 (vii) | Extra query: changes, cancellations or no-shows                                                                             | Hotel, Room, Reservation, ReservationChange, Cancellation | Hotel.HotelID, Hotel.HotelName, Room.HotelID, Reservation.ReservationID, Reservation.ReservationStatus, Reservation.CheckInDate, ReservationChange.ChangeDate, ReservationChange.OriginalCheckInDate, ReservationChange.OriginalCheckOutDate, ReservationChange.NewCheckInDate, ReservationChange.NewCheckOutDate, Cancellation.CancellationDate, Cancellation.CancellationCharge |

## 4.2 Entities and Attributes

### Entity: Hotel

*Represents a hotel branch managed by Global Stay Hotel Group.*

| Attribute | Suggested type | Key | Needed by         | Notes                              |
| --------- | -------------- | --- | ----------------- | ---------------------------------- |
| HotelID   | INT            | PK  | M4-Q4, Q5, Q6, Q7 | Unique hotel identifier            |
| HotelName | VARCHAR(100)   |     | M4-Q4, Q5, Q6, Q7 | Name of the hotel                  |
| Address   | VARCHAR(255)   |     | Hotel             | Hotel address                      |
| City      | VARCHAR(50)    |     | Hotel             | City where the hotel is located    |
| Country   | VARCHAR(50)    |     | Hotel             | Country where the hotel is located |
| Phone     | VARCHAR(20)    |     | Hotel             | Hotel contact number               |
| Email     | VARCHAR(100)   |     | Hotel             | Hotel contact email                |

### Entity: RoomCategory

*Represents a category or type of room offered by the hotel group.*

| Attribute      | Suggested type | Key | Needed by                         | Notes                            |
| -------------- | -------------- | --- | --------------------------------- | -------------------------------- |
| RoomCategoryID | INT            | PK  | ReservationChange, Room, RoomRate | Unique room category identifier  |
| CategoryName   | VARCHAR(30)    |     | Room, RoomRate, ReservationChange | Name of the room category        |
| MaxOccupancy   | INT            |     | RoomCategory                      | Maximum number of occupants      |
| Description    | VARCHAR(255)   |     | RoomCategory                      | Description of the room category |

### Entity: Room

*Represents an individual room in a hotel.*

| Attribute      | Suggested type | Key | Needed by                        | Notes                                                                           |
| -------------- | -------------- | --- | -------------------------------- | ------------------------------------------------------------------------------- |
| RoomID         | INT            | PK  | ReservationRoom, RoomMaintenance | Unique room identifier                                                          |
| HotelID        | INT            | FK  | M4-Q4, Q5, Q6, Q7                | References Hotel                                                                |
| RoomCategoryID | INT            | FK  | RoomCategory, RoomRate           | References RoomCategory                                                         |
| RoomNumber     | VARCHAR(10)    |     | Room                             | Room number within the hotel                                                    |
| FloorNumber    | INT            |     | Room                             | Floor on which the room is located; confirm whether required                    |
| IsAccessible   | BIT            |     | Room                             | Indicates whether the room has accessibility features; confirm whether required |

### Entity: RoomRate

*Represents the nightly rate for a room category at a particular hotel on a specific date.*

| Attribute      | Suggested type | Key | Needed by       | Notes                          |
| -------------- | -------------- | --- | --------------- | ------------------------------ |
| RoomRateID     | INT            | PK  | ReservationRoom | Unique room rate identifier    |
| HotelID        | INT            | FK  | RoomRate        | References Hotel               |
| RoomCategoryID | INT            | FK  | RoomRate        | References RoomCategory        |
| RateDate       | DATE           |     | RoomRate        | Date on which the rate applies |
| NightlyRate    | DECIMAL(10,2)  |     | RoomRate        | Price per night                |

### Entity: Guest

*Represents a guest who makes a reservation or stays at a hotel.*

| Attribute   | Suggested type | Key | Needed by                            | Notes                                           |
| ----------- | -------------- | --- | ------------------------------------ | ----------------------------------------------- |
| GuestID     | INT            | PK  | M4-Q3, Reservation, ReservationGuest | Unique guest identifier                         |
| FirstName   | VARCHAR(50)    |     | M4-Q3                                | Guest's first name                              |
| LastName    | VARCHAR(50)    |     | M4-Q3                                | Guest's last name                               |
| DateOfBirth | DATE           |     | Guest                                | Guest's date of birth; confirm whether required |
| Gender      | VARCHAR(20)    |     | Guest                                | Guest's gender; confirm whether required        |
| Phone       | VARCHAR(20)    |     | Guest                                | Guest's contact number                          |
| Email       | VARCHAR(100)   |     | Guest                                | Guest's email address                           |
| PassportNo  | VARCHAR(30)    |     | Guest                                | Passport number; confirm whether required       |

### Entity: Reservation

*Represents a booking made by a guest for a hotel stay.*

| Attribute         | Suggested type | Key | Needed by                     | Notes                                                        |
| ----------------- | -------------- | --- | ----------------------------- | ------------------------------------------------------------ |
| ReservationID     | INT            | PK  | M4-Q1, Q2, Q3, Q4, Q5, Q6, Q7 | Unique reservation identifier                                |
| GuestID           | INT            | FK  | M4-Q3                         | References Guest who makes the reservation                   |
| ReservationDate   | DATETIME       |     | Reservation                   | Date and time the reservation was made                       |
| CheckInDate       | DATE           |     | M4-Q1, Q4, Q6, Q7             | Scheduled check-in date                                      |
| CheckOutDate      | DATE           |     | M4-Q2, Q7                     | Scheduled check-out date                                     |
| ReservationStatus | VARCHAR(20)    |     | M4-Q4, Q7                     | Reservation status, such as Confirmed, Cancelled, or No-show |
| CheckInDeadline   | DATETIME       |     | M4-Q1, Q4                     | Latest permitted check-in time                               |
| ActualCheckIn     | DATETIME       |     | Reservation                   | Actual check-in date and time; NULL if not checked in        |
| ActualCheckOut    | DATETIME       |     | Reservation                   | Actual check-out date and time; NULL if not checked out      |

### Entity: ReservationRoom

*Represents a room assigned to a reservation, including its rate and room-specific charges.*

| Attribute         | Suggested type | Key | Needed by         | Notes                                       |
| ----------------- | -------------- | --- | ----------------- | ------------------------------------------- |
| ReservationRoomID | INT            | PK  | ReservationGuest  | Unique reservation-room record identifier   |
| ReservationID     | INT            | FK  | M4-Q1, Q4, Q5, Q6 | References Reservation                      |
| RoomID            | INT            | FK  | M4-Q4, Q5, Q6, Q7 | References Room                             |
| RoomRateID        | INT            | FK  | ReservationRoom   | References RoomRate used for the booking    |
| NumberOfNights    | INT            |     | ReservationRoom   | Number of nights booked                     |
| RoomCharge        | DECIMAL(10,2)  |     | M4-Q1             | Room charge for the reservation-room record |
| ExtraBedRequired  | BIT            |     | ReservationRoom   | Indicates whether an extra bed is required  |
| BabyCotRequired   | BIT            |     | ReservationRoom   | Indicates whether a baby cot is required    |

### Entity: ReservationGuest

*Associates guests with a specific room booking and identifies their role in that booking.*

| Attribute          | Suggested type | Key | Needed by        | Notes                                               |
| ------------------ | -------------- | --- | ---------------- | --------------------------------------------------- |
| ReservationGuestID | INT            | PK  | ReservationGuest | Unique reservation-guest record identifier          |
| ReservationRoomID  | INT            | FK  | ReservationGuest | References ReservationRoom                          |
| GuestID            | INT            | FK  | ReservationGuest | References Guest                                    |
| GuestType          | VARCHAR(10)    |     | ReservationGuest | Identifies the guest's role, such as Adult or Child |

### Entity: Service

*Represents an additional service offered by a hotel, such as breakfast or airport transfer.*

| Attribute     | Suggested type | Key | Needed by          | Notes                                              |
| ------------- | -------------- | --- | ------------------ | -------------------------------------------------- |
| ServiceID     | INT            | PK  | ReservationService | Unique service identifier                          |
| HotelID       | INT            | FK  | ReservationService | References Hotel offering the service              |
| ServiceName   | VARCHAR(100)   |     | ReservationService | Name of the service                                |
| ServiceType   | VARCHAR(50)    |     | ReservationService | Category of the service                            |
| ChargingBasis | VARCHAR(20)    |     | ReservationService | Charging method, such as per person or per night   |
| UnitPrice     | DECIMAL(10,2)  |     | ReservationService | Standard price per unit                            |
| IsActive      | BIT            |     | Service            | Indicates whether the service is currently offered |

### Entity: ReservationService

*Associates additional services with a reservation and records the quantity and charge.*

| Attribute            | Suggested type | Key | Needed by          | Notes                                        |
| -------------------- | -------------- | --- | ------------------ | -------------------------------------------- |
| ReservationServiceID | INT            | PK  | ReservationService | Unique reservation-service record identifier |
| ReservationID        | INT            | FK  | ReservationService | References Reservation                       |
| ServiceID            | INT            | FK  | ReservationService | References Service                           |
| Quantity             | INT            |     | ReservationService | Number of service units purchased            |
| UnitPrice            | DECIMAL(10,2)  |     | ReservationService | Price per unit charged for the reservation   |
| ServiceCharge        | DECIMAL(10,2)  |     | ReservationService | Total charge for the service line            |

### Entity: ReservationChange

*Records changes made to a reservation, including the original and revised stay dates and room category.*

| Attribute              | Suggested type | Key | Needed by         | Notes                                         |
| ---------------------- | -------------- | --- | ----------------- | --------------------------------------------- |
| ChangeID               | INT            | PK  | M4-Q2, Q7         | Unique reservation change identifier          |
| ReservationID          | INT            | FK  | M4-Q2, Q7         | References Reservation                        |
| ChangeDate             | DATETIME       |     | M4-Q2, Q7         | Date and time the change was made             |
| OriginalCheckInDate    | DATE           |     | M4-Q2, Q7         | Check-in date before the change               |
| OriginalCheckOutDate   | DATE           |     | M4-Q2, Q7         | Check-out date before the change              |
| NewCheckInDate         | DATE           |     | M4-Q2, Q7         | Revised check-in date                         |
| NewCheckOutDate        | DATE           |     | M4-Q2, Q7         | Revised check-out date                        |
| OriginalRoomCategoryID | INT            | FK  | ReservationChange | References RoomCategory before the change     |
| NewRoomCategoryID      | INT            | FK  | ReservationChange | References RoomCategory after the change      |
| PriceDifference        | DECIMAL(10,2)  |     | ReservationChange | Difference in price resulting from the change |

### Entity: Cancellation

*Records the cancellation of a reservation and any applicable cancellation charge.*

| Attribute          | Suggested type | Key | Needed by             | Notes                                       |
| ------------------ | -------------- | --- | --------------------- | ------------------------------------------- |
| CancellationID     | INT            | PK  | Cancellation          | Unique cancellation record identifier       |
| ReservationID      | INT            | FK  | M4-Q1, Q3, Q5, Q6, Q7 | References Reservation                      |
| CancellationDate   | DATETIME       |     | M4-Q1, Q3, Q5, Q6, Q7 | Date and time the reservation was cancelled |
| CancellationReason | VARCHAR(255)   |     | Cancellation          | Reason for cancellation                     |
| CancellationCharge | DECIMAL(10,2)  |     | M4-Q1, Q7             | Applicable cancellation charge              |

### Entity: Payment

*Records payments and refunds associated with a reservation.*

| Attribute     | Suggested type | Key | Needed by | Notes                              |
| ------------- | -------------- | --- | --------- | ---------------------------------- |
| PaymentID     | INT            | PK  | Payment   | Unique payment record identifier   |
| ReservationID | INT            | FK  | Payment   | References Reservation             |
| PaymentDate   | DATETIME       |     | Payment   | Date and time of payment or refund |
| PaymentAmount | DECIMAL(10,2)  |     | Payment   | Amount paid or refunded            |
| PaymentMethod | VARCHAR(30)    |     | Payment   | Method used for the transaction    |
| PaymentType   | VARCHAR(20)    |     | Payment   | Identifies a payment or refund     |
| ReferenceNo   | VARCHAR(50)    |     | Payment   | Transaction reference number       |

### Entity: RoomMaintenance

*Records maintenance activities carried out on hotel rooms.*

| Attribute              | Suggested type | Key | Needed by       | Notes                                         |
| ---------------------- | -------------- | --- | --------------- | --------------------------------------------- |
| MaintenanceID          | INT            | PK  | RoomMaintenance | Unique maintenance record identifier          |
| RoomID                 | INT            | FK  | RoomMaintenance | References Room                               |
| MaintenanceStartDate   | DATE           |     | RoomMaintenance | Date maintenance starts                       |
| ExpectedCompletionDate | DATE           |     | RoomMaintenance | Expected maintenance completion date          |
| ActualCompletionDate   | DATE           |     | RoomMaintenance | Actual completion date; NULL if not completed |
| MaintenanceReason      | VARCHAR(255)   |     | RoomMaintenance | Reason for maintenance                        |
| MaintenanceStatus      | VARCHAR(20)    |     | RoomMaintenance | Current status of maintenance                 |

## 4.3 Relationships

| Entity A        | Relationship (verb)         | Entity B           | Cardinality | Optionality                                                                                                                         | Needed by             |
| --------------- | --------------------------- | ------------------ | ----------- | ----------------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| Hotel           | has                         | Room               | 1:M         | A Hotel may have zero or many Rooms; each Room belongs to exactly one Hotel                                                         | M4-Q4, Q5, Q6, Q7     |
| RoomCategory    | classifies                  | Room               | 1:M         | A RoomCategory may classify zero or many Rooms; each Room has exactly one RoomCategory                                              | Room, RoomRate        |
| Hotel           | offers                      | RoomRate           | 1:M         | A Hotel may have zero or many RoomRates; each RoomRate belongs to exactly one Hotel                                                 | RoomRate              |
| RoomCategory    | has                         | RoomRate           | 1:M         | A RoomCategory may have zero or many RoomRates; each RoomRate applies to exactly one RoomCategory                                   | RoomRate              |
| Guest           | makes                       | Reservation        | 1:M         | A Guest may make zero or many Reservations; each Reservation has exactly one booking Guest                                          | M4-Q3                 |
| Reservation     | includes                    | ReservationRoom    | 1:M         | A Reservation may have one or many ReservationRoom records; each ReservationRoom belongs to exactly one Reservation                 | M4-Q1, Q4, Q5, Q6     |
| Room            | is assigned to              | ReservationRoom    | 1:M         | A Room may appear in zero or many ReservationRoom records over time; each ReservationRoom references exactly one Room               | M4-Q4, Q5, Q6, Q7     |
| RoomRate        | is used by                  | ReservationRoom    | 1:M         | A RoomRate may be used by zero or many ReservationRoom records; each ReservationRoom references one RoomRate                        | ReservationRoom       |
| ReservationRoom | includes                    | ReservationGuest   | 1:M         | A ReservationRoom may have zero or many ReservationGuest records; each ReservationGuest references exactly one ReservationRoom      | ReservationGuest      |
| Guest           | is associated with          | ReservationGuest   | 1:M         | A Guest may appear in zero or many ReservationGuest records; each ReservationGuest references exactly one Guest                     | ReservationGuest      |
| Hotel           | offers                      | Service            | 1:M         | A Hotel may offer zero or many Services; each Service belongs to exactly one Hotel                                                  | ReservationService    |
| Reservation     | requests                    | ReservationService | 1:M         | A Reservation may have zero or many ReservationService records; each ReservationService belongs to exactly one Reservation          | ReservationService    |
| Service         | is included in              | ReservationService | 1:M         | A Service may appear in zero or many ReservationService records; each ReservationService references exactly one Service             | ReservationService    |
| Reservation     | has                         | ReservationChange  | 1:M         | A Reservation may have zero or many ReservationChange records; each ReservationChange belongs to exactly one Reservation            | M4-Q2, Q7             |
| RoomCategory    | is the original category in | ReservationChange  | 1:M         | A RoomCategory may appear in zero or many changes as the original category; each applicable change references one original category | ReservationChange     |
| RoomCategory    | is the revised category in  | ReservationChange  | 1:M         | A RoomCategory may appear in zero or many changes as the revised category; each applicable change references one revised category   | ReservationChange     |
| Reservation     | has                         | Cancellation       | 1:0..1      | A Reservation may have zero or one Cancellation record; each Cancellation record refers to exactly one Reservation                  | M4-Q1, Q3, Q5, Q6, Q7 |
| Reservation     | has                         | Payment            | 1:M         | A Reservation may have zero or many Payment records; each Payment record belongs to exactly one Reservation                         | Payment               |
| Room            | undergoes                   | RoomMaintenance    | 1:M         | A Room may have zero or many RoomMaintenance records; each RoomMaintenance record refers to exactly one Room                        | RoomMaintenance       |



## 4.4 Assumptions and Open Questions

| #    | Type       | Item                                                                                                                  | Raised for (Q or entity)                     | Resolution                                                                                                                                              |
| ---- | ---------- | --------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 4.1  | Assumption | Each hotel has a unique HotelID.                                                                                      | Hotel                                        | Use HotelID as the primary key.                                                                                                                         |
| 4.2  | Assumption | Each room belongs to one hotel and one room category.                                                                 | Room                                         | Use HotelID and RoomCategoryID as foreign keys.                                                                                                         |
| 4.3  | Question   | Is RoomCategory required as a separate entity?                                                                        | Room, RoomCategory, RoomRate                 | Keep it separate if room categories are shared across rooms and rates depend on room category. Confirm against the assignment requirements.             |
| 4.4  | Assumption | A guest can make multiple reservations, but each reservation has one booking guest.                                   | Guest, Reservation; M4-Q3                    | Use GuestID as a foreign key in Reservation. Use ReservationGuest to associate additional guests with booked rooms.                                     |
| 4.5  | Assumption | A reservation may contain multiple rooms.                                                                             | Reservation, ReservationRoom                 | Use ReservationRoom as an associative entity between Reservation and Room.                                                                              |
| 4.6  | Assumption | A room can be booked by different reservations over time, but overlapping confirmed stays must not use the same room. | ReservationRoom                              | Apply booking validation to prevent overlapping room assignments. Consider check-in and check-out dates when determining overlap.                       |
| 4.7  | Question   | Can one reservation have multiple cancellation records?                                                               | Reservation, Cancellation; M4-Q1, Q3, Q5, Q6 | Assume one cancellation record per reservation unless the brief requires cancellation history.                                                          |
| 4.8  | Assumption | A cancellation record stores the cancellation date, reason, and applicable cancellation charge.                       | Cancellation; M4-Q1                          | Use CancellationDate and CancellationCharge. Calculate the charge according to the cancellation policy.                                                 |
| 4.9  | Assumption | A cancellation is considered late if it occurs less than 48 hours before the scheduled check-in time.                 | Reservation, Cancellation; M4-Q1             | Compare CancellationDate with the scheduled check-in date and time. Confirm whether CheckInDeadline or another check-in time is specified in the brief. |
| 4.10 | Assumption | Reservation changes must preserve the original and revised stay dates.                                                | ReservationChange; M4-Q2                     | Store the original and new check-in and check-out dates, together with ChangeDate.                                                                      |
| 4.11 | Question   | Must room-category changes be recorded even when only the stay dates change?                                          | ReservationChange                            | Store the original and revised room categories when applicable. Allow the category fields to be NULL if category changes are optional.                  |
| 4.12 | Assumption | A no-show is recorded using ReservationStatus rather than a separate NoShow entity.                                   | Reservation; M4-Q4, Q7                       | Use a consistent status value such as 'No-show'. ActualCheckIn should remain NULL when the guest did not check in.                                      |
| 4.13 | Question   | How should no-shows be attributed to a hotel if a reservation has multiple rooms or hotels?                           | Hotel, ReservationRoom; M4-Q4                | Count no-shows using the room assignment and hotel associated with each reservation. Define how multi-hotel reservations should be counted.             |
| 4.14 | Question   | What is the denominator for the annual cancellation rate?                                                             | Hotel, Reservation, Cancellation; M4-Q6      | Confirm the definition in the brief. A possible formula is cancelled reservations divided by eligible reservations, multiplied by 100.                  |
| 4.15 | Assumption | A reservation can have multiple payment transactions, including refunds.                                              | Reservation, Payment                         | Use PaymentType to distinguish payments from refunds. Store each transaction as a separate Payment record.                                              |
| 4.16 | Assumption | Additional services are optional for each reservation.                                                                | Reservation, ReservationService, Service     | A reservation may have zero or many ReservationService records.                                                                                         |
| 4.17 | Question   | Are floor number, accessibility, gender, date of birth, and passport number required?                                 | Room, Guest                                  | Retain these attributes only if required by the case study or justified by the system requirements.                                                     |
| 4.18 | Question   | Is room maintenance within the assignment scope?                                                                      | RoomMaintenance                              | Keep RoomMaintenance only if the requirements include room maintenance management.                                                                      |
| 4.19 | Assumption | Room rates may vary by hotel, room category, and date.                                                                | RoomRate                                     | Use HotelID, RoomCategoryID, and RateDate to identify the applicable rate. Add a unique constraint if only one rate is allowed for each combination.    |
| 4.20 | Question   | Should a reservation's room charge be stored or calculated from the nightly rate and number of nights?                | ReservationRoom                              | Confirm the business rules. If storing RoomCharge, ensure it represents the agreed charge and remains consistent with the applicable rate.              |
| 4.21 | Assumption | Each room number must be unique within its hotel, but different hotels may reuse the same room number.                | Room                                         | Consider a unique constraint on (HotelID, RoomNumber).                                                                                                  |
| 4.22 | Question   | Should cancellation counts include only confirmed reservations, or all reservations that were cancelled?              | Hotel, Reservation, Cancellation; M4-Q5, Q6  | Define the eligible reservation statuses consistently before calculating cancellation counts and rates.                                                 |
| 4.23 | Assumption | Date-range reports use clearly defined inclusive start and end dates.                                                 | M4-Q1, Q2, Q4, Q5, Q6                        | Use consistent date filtering and clarify whether date ranges refer to booking dates, check-in dates, change dates, or cancellation dates.              |



## 4.5 Ideas Parking Lot (Optional)

* `[constraint]` Add a CHECK constraint to ensure CheckOutDate is later than CheckInDate.
* `[constraint]` Add a CHECK constraint to prevent negative or zero NightlyRate, Quantity, and NumberOfNights values where the business rules require positive values.
* `[constraint]` Add a CHECK constraint to prevent negative RoomCharge and CancellationCharge values unless the business rules explicitly permit adjustments.
* `[constraint]` Add a CHECK constraint to restrict ReservationStatus to valid values, such as Pending, Confirmed, Cancelled, Checked-in, Checked-out, and No-show.
* `[constraint]` Add a CHECK constraint to restrict PaymentType to valid values, such as Payment and Refund.
* `[constraint]` Add a CHECK constraint to ensure ExpectedCompletionDate is not earlier than MaintenanceStartDate.
* `[constraint]` Add a UNIQUE constraint on (HotelID, RoomNumber) to prevent duplicate room numbers within the same hotel.
* `[constraint]` Consider a UNIQUE constraint on (HotelID, RoomCategoryID, RateDate) if only one room rate is permitted for each hotel, category, and date.
* `[constraint]` Add foreign keys for all relationships and decide appropriate ON DELETE and ON UPDATE actions based on the business rules.
* `[procedure]` Create a stored procedure to create a reservation and assign one or more rooms within a transaction.
* `[procedure]` Create a stored procedure to calculate cancellation charges based on the time remaining before check-in and the applicable cancellation policy.
* `[procedure]` Create a stored procedure to process reservation date or room-category changes and record the original and revised details in ReservationChange.
* `[procedure]` Create a stored procedure to calculate annual cancellation counts and cancellation rates by hotel.
* `[trigger]` Consider a trigger to record reservation changes automatically if changes must be audited. Alternatively, use stored procedures to ensure changes are recorded consistently.
* `[trigger]` Consider a trigger to validate room assignments if overlapping bookings cannot be reliably prevented by the application's booking process alone.
* `[optimisation]` Add indexes on frequently filtered or joined columns, such as Reservation.CheckInDate, Reservation.ReservationStatus, Cancellation.CancellationDate, ReservationChange.ChangeDate, and foreign keys.
* `[optimisation]` Use a consistent date-range filtering pattern for monthly and yearly reports to improve correctness and index usage.
* `[optimisation]` Use conditional aggregation or GROUPING SETS / ROLLUP for the annual cancellation report with hotel subtotals and a grand total (M4-Q5).
* `[optimisation]` Use a clearly defined numerator and denominator when calculating cancellation rates to avoid inconsistent comparisons between hotels (M4-Q6).
* `[other]` Define whether a cancellation charge is stored as a historical amount at cancellation time or recalculated whenever the cancellation policy changes.
* `[other]` Define whether room rates are per night, per room, per guest, or based on another pricing rule.
* `[other]` Define how reservations spanning multiple hotels should be handled in hotel-level cancellation and no-show reports.
* `[other]` Review all proposed entities and attributes against the assignment brief and remove any that are not required.

<!-- SECTION 4 END -->

---

<!-- SECTION 5 START: MEMBER 5 -->

## Section 5: Member 5 - Billing, payments and maintenance

**Status:** `In Progress`

### 5.1 Question trace

Fill the last two columns for every query before drawing entities.

| Q           | Query (short)                                                                                       | Entities touched                                         | Attributes needed                                                                                                                                                                                                                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------- | -------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| M5-Q1 (i)   | Room charges, service charges, discounts, payments and outstanding balance for a reservation        | Reservation, RoomAssignment, ReservationService, Payment | Reservation.ReservationID, Reservation.DiscountAmount, RoomAssignment.ReservationID, RoomAssignment.RoomChargeAmount, ReservationService.ReservationID, ReservationService.ServiceChargeAmount, Payment.ReservationID, Payment.PaymentAmount, Payment.PaymentType. Calculated: OutstandingBalance                           |
| M5-Q2 (ii)  | Checked-out reservations that still have an outstanding balance                                     | Reservation, RoomAssignment, ReservationService, Payment | Reservation.ReservationID, Reservation.Status, Reservation.DiscountAmount, RoomAssignment.ReservationID, RoomAssignment.RoomChargeAmount, ReservationService.ReservationID, ReservationService.ServiceChargeAmount, Payment.ReservationID, Payment.PaymentAmount, Payment.PaymentType, Calculated: OutstandingBalance (> 0) |
| M5-Q3 (iii) | Rooms currently under maintenance: hotel name, room number, start date, expected completion         | Maintenance, Room, Hotel                                 | Maintenance.RoomID, Maintenance.StartDate, Maintenance.ExpectedCompletionDate, Room.RoomID, Room.HotelID, Room.RoomNumber, Hotel.HotelID, Hotel.HotelName                                                                                                                                                                   |
| M5-Q4 (iv)  | Rooms with no completed stays in a date range                                                       | Room, RoomAssignment, Reservation                        | Room.RoomID, RoomAssignment.RoomID, RoomAssignment.ReservationID, Reservation.ReservationID, Reservation.Status, Reservation.CheckInDate, Reservation.CheckOutDate                                                                                                                                                          |
| M5-Q5 (v)   | Total payments by hotel and payment method in a month, with subtotals and grand total (ROLLUP/CUBE) | Payment, Reservation, Hotel                              | Payment.ReservationID, Payment.PaymentAmount, Payment.PaymentDate, Payment.PaymentMethod, Payment.PaymentType, Reservation.ReservationID, Reservation.HotelID, Hotel.HotelID, Hotel.HotelName                                                                                                                               |
| M5-Q6 (vi)  | Hotel with highest revenue from completed stays in a quarter                                        | Hotel, Reservation, RoomAssignment, ReservationService   | Hotel.HotelID, Hotel.HotelName, Reservation.ReservationID, Reservation.HotelID, Reservation.Status, Reservation.CheckOutDate, Reservation.DiscountAmount, RoomAssignment.ReservationID, RoomAssignment.RoomChargeAmount, ReservationService.ReservationID, ReservationService.ServiceChargeAmount. Calculated: Revenue      |
| M5-Q7 (vii) | Extra query: billing, payments or maintenance                                                       | Maintenance, Room, Hotel                                 | Maintenance.MaintenanceID, Maintenance.RoomID, Maintenance.StartDate, Maintenance.ExpectedCompletionDate, Room.RoomID, Room.HotelID, Hotel.HotelID, Hotel.HotelName. Calculated: downtime days                                                                                                                              |

### 5.2 Entities and attributes

<!-- One table per entity. -->

#### Entity: Hotel

_<what a row represents>_

| Attribute | Suggested type | Key | Needed by | Notes |
| --------- | -------------- | --- | --------- | ----- |
| HotelID   | INT            | PK  |           |       |
| HotelName | VARCHAR(100)   |     |           |       |

#### Entity: Room

_<what a row represents>_

| Attribute  | Suggested type | Key                 | Needed by | Notes |
| ---------- | -------------- | ------------------- | --------- | ----- |
| RoomID     | INT            | PK                  |           |       |
| HotelID    | INT            | FK -> Hotel.HotelID |           |       |
| RoomNumber | VARCHAR(10)    |                     |           |       |

#### Entity: Reservation

_<what a row represents>_

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

_a bridge between Reservation and OptionalService (M:M relationship)_

| Attribute           | Suggested type | Key                                         | Needed by | Notes |
| ------------------- | -------------- | ------------------------------------------- | --------- | ----- |
| ReservationID       | INT            | PK, FK -> Reservation.ReservationID         |           |       |
| OptionalServiceID   | INT            | PK, FK -> OptionalService.OptionalServiceID |           |       |
| ServiceChargeAmount | DECIMAL(12,2)  |                                             |           |       |

#### Entity: Payment

_<what a row represents>_

| Attribute     | Suggested type | Key                             | Needed by | Notes                                          |
| ------------- | -------------- | ------------------------------- | --------- | ---------------------------------------------- |
| PaymentID     | INT            | PK                              |           |                                                |
| ReservationID | INT            | FK -> Reservation.ReservationID |           |                                                |
| PaymentDate   | DATE           |                                 |           |                                                |
| PaymentAmount | DECIMAL(12,2)  |                                 |           | > 0                                            |
| PaymentMethod | VARCHAR(20)    |                                 |           | Cash, Credit Card, Debit Card, Online Transfer |
| PaymentType   | VARCHAR(20)    |                                 |           | Payment, Refund                                |

#### Entity: Maintenance

_<what a row represents>_

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

- \[constraint]: `CHECK (PaymentAmount > 0)`, `CHECK (PaymentType IN ('Payment','Refund'))`, and `CHECK (ExpectedCompletionDate >= StartDate)`.
- \[procedure]: `sp_GetReservationBill(@ReservationID)`, returning the itemised bill and balance.
- \[trigger]: Block a Payment that would overpay the balance, or a refund larger than the payments received.
- \[optimisation]:
- \[other]:
