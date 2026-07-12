# ADR-001: Separate C# Backend and React Frontend

**Status**: Accepted

## Context
The project needs a technology stack that supports learning goals 
(C# and React) while remaining realistic for professional environments.

## Decision
Use ASP.NET Core Web API (C#, controller-based) as a standalone backend, 
and React with Vite as a standalone frontend, communicating via REST API.

## Alternatives considered
- **Full-stack JS framework (e.g. Next.js)**: rejected — doesn't align 
  with the goal of practicing C#
- **Backend in Node.js**: rejected — same reason; C# was the intended 
  learning focus

## Consequences
Two separate codebases to maintain and deploy, but clearer separation of 
concerns and alignment with common enterprise patterns (separate 
frontend/backend teams).