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

[ Users ] 1 ────────── 0..* [ Bookings ]
                                     *
                                     │
                                     │ 0..*
                                     1
                                [ Matches ]