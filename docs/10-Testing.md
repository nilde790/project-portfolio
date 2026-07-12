# Testing Strategy

## Backend (C#)

### Unit tests — xUnit + Moq
Test the business logic inside **Services**, in isolation from the 
database. Repository dependencies are **mocked** (via Moq) using the 
existing `Interfaces` (e.g. `IListingRepository`), rather than hitting 
a real PostgreSQL instance.

Covered:
- Business rules (e.g. "a user cannot edit another user's listing", 
  "a listing cannot have more than 5 images")
- Input validation logic
- Edge cases (e.g. negative price, empty required fields)

Not covered by unit tests:
- Actual EF Core query correctness (see Integration tests below)
- Controller routing/HTTP behavior (see Integration tests below)

### Integration tests
Test the interaction between Services/Repositories and a real 
(ephemeral) PostgreSQL instance, typically run against a disposable 
Docker container created and destroyed for the test run — not the 
development database.

Covered:
- EF Core queries return expected results against real schema
- End-to-end API behavior (e.g. `POST /api/listings` → row actually 
  created, correctly linked to the authenticated user)

## Frontend (React)

### Unit/component tests — Vitest + React Testing Library
Test components by simulating real user interaction (clicking, typing, 
reading rendered output), not internal implementation details.

Covered:
- Forms validate input correctly before submission (e.g. 
  `CreateListingForm` blocks submit with empty title)
- Conditional rendering (e.g. "Sold" badge appears when listing status 
  is `sold`)
- Basic hook behavior where feasible (e.g. `useAuth` reflects login state)

Not covered in v1:
- End-to-end browser tests (e.g. Cypress/Playwright) — considered a 
  possible v2 addition if time allows, not essential for this project's 
  learning goals

## What is intentionally NOT tested

- Third-party integrations are not re-tested (e.g. we trust Cloudinary's 
  own reliability; we only test that our code calls it correctly)
- Purely visual/styling correctness (no automated visual regression testing)

## CI integration

Both backend and frontend test suites run automatically via GitHub 
Actions on every push/pull request (see 06-Architecture.md → CI/CD), 
and must pass before merging to `main`.