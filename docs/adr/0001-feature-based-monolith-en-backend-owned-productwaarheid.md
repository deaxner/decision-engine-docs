# ADR 0001: Feature-Based Monolith And Backend-Owned Product Truth

## Status

Accepted

## Context

The product is implemented across separate repositories for API, web, infra, and docs. That split can create a false impression that the backend should fragment into microservices or that product rules can safely drift into frontend code and local tooling.

The current product does not need service-level distribution. It needs:

- a stable backend-owned domain model for workspaces, sessions, votes, results, and activity
- a frontend that renders and submits against backend contracts
- local tooling that supports the same model instead of bypassing it
- documentation that explains ownership clearly across repositories

Without a clear architectural decision, the likely failure modes are:

- controller-heavy orchestration quietly becoming the de facto application layer
- console commands and seed scripts growing into parallel business logic paths
- frontend code compensating for malformed or ambiguous backend payloads
- repo boundaries being mistaken for deployable service boundaries

## Decision

We keep the runtime backend as a modular monolith and treat it as the source of product truth.

The practical consequences are:

- `decision-engine-api` owns domain rules, persistence, authorization, read models, and async recomputation.
- `decision-engine-web` owns presentation, browser state, and interaction flow, but not product rules.
- `decision-engine-infra` owns environment/runtime concerns, not application logic.
- `decision-engine-docs` owns cross-repo architecture and decision records.

Inside the backend:

- domain rules belong in domain or application services, not controllers
- console commands are adapters over application services
- external payloads are normalized before entering app state
- feature-oriented boundaries are preferred over generic utility sprawl

## Consequences

### Positive

- Product invariants have a clear owner.
- Frontend and tooling are less likely to fork core business behavior.
- Refactors can move orchestration out of controllers incrementally without changing deployment shape.
- Documentation and code now point to the same ownership model.

### Negative

- The monolith still requires discipline; bad boundaries can still form inside one deployable.
- Some layers remain transitional, especially older controller orchestration paths.
- This decision does not remove the need for explicit contracts and tests; it only clarifies where they belong.

## Follow-up

- Continue moving backend orchestration out of controllers and console commands into named application services.
- Add more explicit mapping/DTO boundaries where raw payloads still leak across layers.
- Keep ADRs current when repository responsibilities or deployment boundaries change.
