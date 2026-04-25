# Repository Boundaries

The runtime architecture remains a modular monolith. The repositories are split by product surface and operational ownership, not by backend domain service.

## Repositories

`decision-engine-api`

Symfony backend modular monolith. Owns domain logic, current controller-level orchestration, persistence, Messenger workers, authentication, authorization, and Mercure publishing.

`decision-engine-web`

Frontend application. Owns the workspace, decision, voting, and live results experience.

`decision-engine-infra`

Infrastructure configuration. Owns Docker Compose, local dev container images, startup scripts, service configuration, deployment notes, and environment examples.

`decision-engine-docs`

Documentation. Owns customer request summaries, architecture, ADRs, API notes, and cross-repo decisions.

## Current shipped responsibilities

`decision-engine-api`

- Authentication and token issuance
- Workspace membership and authorization rules
- Session lifecycle, option management, and vote acceptance
- Session metadata for category/node, due date, and existing-member assignees
- Async result recomputation through Messenger
- Mercure `result_updated` publishing for `/sessions/{id}/results`
- Read-model endpoints used by the web client
- Direct member add for existing registered users by `email` or `user_id`
- Demo data orchestration through application services; console commands are only entrypoints
- HTTP controllers should remain adapters; request mapping, write orchestration, read-model querying, access checks, result computation, async handlers, and response shaping belong in explicit backend boundary/application services, with typed inputs, explicit response outputs, and narrow persistence ports for both reads and writes at the application edge

`decision-engine-web`

- Browser auth state
- Workspace and session navigation
- Session creation and option editing
- Majority and ranked IRV vote submission
- Result fetch and Mercure-driven invalidation/refetch

`decision-engine-infra`

- Local runtime orchestration for `postgres`, `redis`, `mercure`, `api`, `worker`, and `web`
- Shared env examples for local development
- Containerized API, worker, and web development flow

`decision-engine-docs`

- Customer and product context
- Cross-repo setup and ownership notes
- Architecture alignment notes as the implementation evolves

## Current Boundaries And Gaps

The repository split is operational, not a backend service split. `decision-engine-api` remains one deployable modular monolith.

Known gaps are documented rather than implemented in this slice: true invitations, audit read/export surfaces, token expiry/refresh/revocation, comments/discussion, notifications/read state, richer assignment workflows, category taxonomy, and continued tightening of feature boundaries where legacy controller logic still exists.
