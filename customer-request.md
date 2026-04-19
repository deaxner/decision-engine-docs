# Customer Request Summary

The customer wants a real-time decision platform for distributed teams.

The system should replace long Slack threads, unclear voting processes, and manual aggregation with structured decision sessions.

## Required

- Workspaces with isolated teams.
- Member add flow for existing registered users and simple roles.
- Decision sessions with title, options, and voting method.
- Simple majority voting.
- Ranked-choice voting using Instant Runoff Voting.
- Live result updates without refresh or polling.
- Fast vote recording.
- Async result computation so voting is not blocked.
- Auditability for who voted, when they voted, and how results were derived.
- Current winner, vote distribution, and ranked-voting round explanations.

## Shipped MVP interpretation

The current implementation covers the first usable product slice:

- registered users can create workspaces
- workspace owners can add existing registered users by email
- users can create draft sessions, add options, and open or close voting
- votes are stored immediately as immutable rows
- result recomputation happens asynchronously through Messenger
- clients subscribe to Mercure and refetch `GET /sessions/{id}/results` when `result_updated` is published

This keeps the write path fast while preserving the architecture rule that REST remains the source of truth for reconnects and refreshes.

## Remaining Product Gaps

- True invitation lifecycle: invite records, outbound email, pending acceptance, and invite expiry.
- Dedicated audit surfaces: APIs and UI for vote history, active-vote derivation, and final result explanation.
- Token lifecycle hardening: expiry, refresh, and revocation.
- Administrative operations such as export, archival, and workspace member removal.

## Out Of Scope

- Complex permissions.
- Anonymous voting.
- Weighted voting.
- Runtime microservices.
- Real-time collaborative editing.
