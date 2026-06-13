# Football Ticket Booking System ⚽🎟️

A relational database design and intermediate-to-advanced SQL query implementation for a simplified Football Ticket Booking System. This project demonstrates E-R diagramming, data definition (DDL), referential integrity constraints, and complex data retrieval techniques including subqueries, conditional logic, and table joins.

---

## 📌 Project Objectives
- **Database Schema Design:** Formulate structured tables handling one-to-many and conditional relationships.
- **Data Integrity & Constraints:** Implement strict primary keys, foreign keys, unique fields, and value check constraints.
- **Advanced Querying:** Solve real-world data tracking problems using `INNER JOIN`, `LEFT JOIN`, `COALESCE`, pattern matching (`ILIKE`), aggregate subqueries, and windowing clauses (`LIMIT`/`OFFSET`).

---

## 🗺️ Entity-Relationship Diagram (ERD)

The system relies on three core entities:
1. **Users:** Tracks administrative staff and football fans.
2. **Matches:** Catalogs tournament fixtures, pricing structures, and match statuses.
3. **Bookings:** The transactional engine mapping users directly to unique match seat reservations.

## Database Design
- [View ER Diagram](https://drawsql.app/teams/kazi-ashikur/diagrams/a3-football-ticket-booking-system)

[ Users ] 1 ────────── 0..* [ Bookings ]
                                     *
                                     │
                                     │ 0..*
                                     1
                                [ Matches ]




                                *Note: In the database schema, foreign keys (`user_id`, `match_id`) inside the `Bookings` table map data types cleanly across tables using uniform integer tracking.*

---

## 🛠️ Database Schema Setup (DDL)

```sql
-- 1. CREATE USERS TABLE
CREATE TABLE Users (
    user_id SERIAL PRIMARY KEY,
    full_name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    role VARCHAR(20) NOT NULL CHECK (role IN ('Ticket Manager', 'Football Fan')),
    phone_number VARCHAR(20)
);

-- 2. CREATE MATCHES TABLE
CREATE TABLE Matches (
    match_id SERIAL PRIMARY KEY,
    fixture VARCHAR(100) NOT NULL,
    tournament_category VARCHAR(100) NOT NULL,
    base_ticket_price DECIMAL(8,2) CONSTRAINT chk_ticket_price CHECK (base_ticket_price >= 0),
    match_status VARCHAR(20) CONSTRAINT chk_match_status CHECK (match_status IN ('Available', 'Selling Fast', 'Sold Out', 'Postponed'))
);

-- 3. CREATE BOOKINGS TABLE
CREATE TABLE Bookings (
    booking_id SERIAL PRIMARY KEY,
    user_id INT REFERENCES Users(user_id),
    match_id INT REFERENCES Matches(match_id),
    seat_number VARCHAR(15),
    payment_status VARCHAR(50) CONSTRAINT chk_payment_status CHECK (payment_status IN ('Pending', 'Confirmed', 'Cancelled', 'Refunded')),
    total_cost DECIMAL(8,2) CONSTRAINT chk_total_cost CHECK (total_cost >= 0)
);



INSERT INTO Users (user_id, full_name, email, role, phone_number) VALUES
(1, 'Tanvir Rahman', 'tanvir@mail.com', 'Football Fan', '+8801711111111'),
(2, 'Asif Haque', 'asif@mail.com', 'Football Fan', '+8801722222222'),
(3, 'Sajjad Rahman', 'sajjad@mail.com', 'Ticket Manager', '+8801733333333'),
(4, 'Jannat Ara', 'jannat@mail.com', 'Football Fan', NULL);

INSERT INTO Matches (match_id, fixture, tournament_category, base_ticket_price, match_status) VALUES
(101, 'Real Madrid vs Barcelona', 'Champions League', 150.00, 'Available'),
(102, 'Man City vs Liverpool', 'Premier League', 120.00, 'Selling Fast'),
(103, 'Bayern Munich vs PSG', 'Champions League', 130.00, 'Available'),
(104, 'AC Milan vs Inter Milan', 'Serie A', 90.00, 'Sold Out'),
(105, 'Juventus vs Roma', 'Serie A', 80.00, 'Available');

INSERT INTO Bookings (booking_id, user_id, match_id, seat_number, payment_status, total_cost) VALUES
(501, 1, 101, 'A-12', 'Confirmed', 150.00),
(502, 1, 102, 'B-04', 'Confirmed', 120.00),
(503, 2, 101, 'A-13', 'Confirmed', 150.00),
(504, 2, 101, NULL, NULL, 150.00),
(505, 3, 102, 'C-20', 'Pending', 120.00);



## SQL Queries & Implementations


## Query 1: Filter Available Cup Matches Retrieve upcoming matches belonging to the 'Champions League' where the status is 'Available'.

SQL
SELECT * FROM matches 
WHERE tournament_category = 'Champions League' 
  AND match_status = 'Available';



## Query 2: Case-Insensitive Pattern Matching Search for users whose names start with 'Tanvir' or contain 'Haque'.

SQL
SELECT * FROM users  
WHERE full_name ILIKE 'Tanvir%' 
   OR full_name ILIKE '%Haque%';


## Query 3: Graceful Null Resolution Isolate uncompleted payment items and replace blank records with 'Action Required'.

SQL
SELECT booking_id, user_id, match_id, seat_number, 
       COALESCE(payment_status, 'Action Required') AS payment_status, 
       total_cost 
FROM bookings 
WHERE payment_status IS NULL;


## Query 4: Relational Multi-Table Joins Combine user profiles, receipts, and tournament matchups into a single, clean lookup dataset.

SQL
SELECT b.booking_id, u.full_name, m.fixture, b.total_cost 
FROM users u  
INNER JOIN bookings b ON u.user_id = b.user_id  
INNER JOIN matches m ON m.match_id = b.match_id;


## Query 5: Preserving Unmatched Left Rows Generate a master client directory displaying registration numbers alongside any current purchases, ensuring accounts without tickets are preserved.

SQL
SELECT u.user_id, u.full_name, b.booking_id 
FROM users u 
LEFT JOIN bookings b ON u.user_id = b.user_id;


## Query 6: Complex Aggregates via Subqueries Expose high-tier accounts where a ticket invoice cost stretches strictly higher than the platforms global average transactional cost.

SQL
SELECT booking_id, match_id, total_cost 
FROM bookings 
WHERE total_cost > (SELECT AVG(total_cost) FROM bookings);


## Query 7: Dataset Pagination and Offsets Isolate premium ticket match offerings by ordering listings by baseline values, returning the top 2 highest results while intentionally skipping the number one spot.

SQL
SELECT match_id, fixture, base_ticket_price 
FROM matches 
ORDER BY base_ticket_price DESC 
LIMIT 2 OFFSET 1;
