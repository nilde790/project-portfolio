# ADR-003: JWT-Based Authentication

**Status**: Accepted

## Context
The application needs a way to authenticate users across stateless REST 
API requests.

## Decision
Use JWT (JSON Web Tokens): the backend issues a token on login, the 
frontend attaches it to subsequent requests via the Authorization header.

## Alternatives considered
- **Session-based auth (server-side sessions + cookies)**: rejected — 
  requires server-side session storage, less natural fit for a stateless 
  REST API and for separate frontend/backend deployments

## Consequences
The backend doesn't need to store session state, simplifying scaling, 
but token expiration/refresh logic must be handled explicitly.