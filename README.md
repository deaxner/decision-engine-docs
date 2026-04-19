# Decision Engine Docs

Product and technical documentation for the real-time decision platform.

## License Status

This repository is source-available for viewing and evaluation only.
All rights are reserved by the author. No permission is granted to use,
modify, redistribute, or commercialize this material without prior written
permission. See [LICENSE](LICENSE).

## Contents

- Customer request summary.
- Repository boundary decisions.
- Local runtime stack notes.
- Shipped MVP behavior across API, web, and infra.
- Known MVP gaps and documentation drift notes.

## Current State

The implementation is currently spread across four repositories under `deaxner`:

- `decision-engine-api`: Symfony 7 backend modular monolith, PostgreSQL persistence, Messenger async result recompute, Mercure result publishing, and the MVP JSON API.
- `decision-engine-web`: React + Vite + TypeScript MVP client for auth, workspaces, sessions, voting, and live result refresh.
- `decision-engine-infra`: Docker Compose local dev stack for `postgres`, `redis`, `mercure`, `api`, `worker`, and `web`.
- `decision-engine-docs`: cross-repo product and technical documentation.

## Shipped MVP Behavior

The current product slice supports the core decision workflow:

- Register or log in and receive a bearer token.
- Create a workspace; the creator becomes `OWNER`.
- Add existing registered users to a workspace by email.
- Create draft decision sessions with `MAJORITY` or `RANKED_IRV` voting.
- Add options while the session is `DRAFT`.
- Open a session for voting and close it when complete.
- Cast votes as immutable rows; changed votes create new rows and latest vote wins.
- Recompute result snapshots asynchronously through Messenger workers.
- Publish Mercure `result_updated` notifications on `/sessions/{id}/results`.
- Have the web client refetch REST results after Mercure notifications.
- Record product-facing activity events for workspace, member, session, option, voting, vote, close, and result recompute actions.
- Read workspace dashboard analytics from `GET /workspaces/{id}/dashboard`.

REST remains the source of truth. Mercure is used as an invalidation signal only.

## Local Runtime

The local development entrypoint is `decision-engine-infra`.

Default host ports:

- Web: `http://127.0.0.1:5173`
- API: `http://127.0.0.1:8000`
- Mercure: `http://127.0.0.1:3001/.well-known/mercure`
- PostgreSQL: `5432`
- Redis: `6379`

Runtime responsibilities:

- `api` persists commands and serves read endpoints
- `worker` consumes Messenger async jobs for result recomputation
- `mercure` pushes `result_updated` events
- `web` consumes REST endpoints and refetches results after Mercure notifications

## Setup Entrypoint

Use `decision-engine-infra` for local development:

```bash
cp .env.example .env
docker compose up --build
```

The Compose stack bind-mounts the sibling `decision-engine-api` and `decision-engine-web` repositories. The API and worker containers install dependencies, wait for PostgreSQL, run migrations, and then serve Symfony or consume Messenger jobs.

## Known Gaps

- Member add is not a true invitation system; it only adds already registered users by `email` or legacy `user_id`.
- Dashboard activity is stored and displayed, but there is no dedicated audit export endpoint yet.
- Auth is MVP-grade bearer token auth without token expiry, refresh, or revocation.
- Decision categories/nodes, due dates, assigned stakeholders, comments, unread notifications, and real notification settings remain future product work.
- Application orchestration is partly in controllers; fully separate command/use-case classes remain a future refactor.
- Demo seed data includes workspaces, members, decisions, options, votes, result snapshots, and dashboard activity rows.
