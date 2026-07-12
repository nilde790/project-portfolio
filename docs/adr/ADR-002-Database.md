# ADR-002: PostgreSQL with UUID Primary Keys

**Status**: Accepted

## Context
The project needs a relational database with good ecosystem support and 
free-tier hosting compatibility.

## Decision
Use PostgreSQL as the database engine, with UUID as the primary key type 
across all tables.

## Alternatives considered
- **MySQL**: rejected — PostgreSQL has stronger adoption in modern 
  backend ecosystems and equally good free-tier hosting options
- **SERIAL (auto-increment integers)**: rejected in favor of UUID — 
  sequential IDs expose record-count information (e.g. `/listings/47`) 
  and are less consistent with distributed-system patterns

## Consequences
UUIDs are slightly heavier to store/index than integers, but this is 
negligible at this project's scale, and the practice is valuable for 
learning modern API design patterns.