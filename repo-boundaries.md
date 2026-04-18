# Repository Boundaries

The runtime architecture remains a modular monolith. The repositories are split by product surface and operational ownership, not by backend domain service.

## Repositories

`decision-engine-api`

Symfony backend modular monolith. Owns domain logic, use cases, persistence, Messenger workers, authentication, authorization, and Mercure publishing.

`decision-engine-web`

Frontend application. Owns the workspace, decision, voting, and live results experience.

`decision-engine-infra`

Infrastructure configuration. Owns Docker Compose, service configuration, deployment notes, and environment examples.

`decision-engine-docs`

Documentation. Owns customer request summaries, architecture, ADRs, API notes, and cross-repo decisions.

