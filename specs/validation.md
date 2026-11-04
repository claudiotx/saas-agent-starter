# Validation

> **Example content** for the made-up SignalDeck project. Agent 1 (Requirements Analyst) writes
> this file at the end of Step 1. Agent 2 (Manager) refuses to start unless the status is
> `READY`. Replace everything below for your project.

## Status

**Status: READY**

Validated by: Agent 1, on 2026-01-01 (example date; write the real date).

## Checks

| Check | Result | Evidence |
|---|---|---|
| Clarity: each FR and NFR has one reading and a testable outcome | Pass | Every NFR has a number or pass/fail rule (NFR-1 to NFR-8). |
| Consistency: no conflicting requirements | Pass | "Offline for 72 h" (NFR-1) and "60 s updates" (NFR-2) apply to different states (offline vs online). |
| Traceability: every must-have FR is in a roadmap task | Pass | FR-1 to FR-8 map to Phases 2 to 6. |
| Traceability: every roadmap task cites an FR or NFR | Pass | Each phase header lists its IDs. |
| Technical feasibility: stack meets every NFR | Pass | Service workers and IndexedDB support 72 h cache (checked against current browser docs). 1,000 screens polling every 30 s is about 33 requests per second, within a single small instance. |
| Known limits | Pass | Browser storage quota can evict cache; player requests persistent storage and the roadmap test covers it. |
| Dependencies listed with owner and status | Pass | `data-sources.md` lists S3, Stripe, email, each with a dev substitute. |
| Environment: tools run here | Pass | `pnpm install`, `docker compose up` and a Playwright hello-world ran successfully. |
| Ordering: no task needs a later task | Pass | Reviewed Phases 1 to 8 in order. |
| Size: every task is worker-sized with a "done when" | Pass | Each task names a command or test. |

## Open questions

None. (Must be empty for `READY`.)

## Assumptions

| Assumption | Risk if wrong | Where it lives |
|---|---|---|
| Plan price is 9 USD per screen per month | Low. A price ID in config. | `data-sources.md` |
| H.264 MP4 only, no transcoding | Medium. Customers with other formats see an error. | `tech-stack.md` |
| English only, single region | Low for v1. | `mission.md` |
| Stripe and email test keys are provided before Phase 2 and 7, or substitutes are used | Medium. Real billing and email unverified until release. | `data-sources.md` |

## Template for your project

Keep these sections: Status, Checks (one row per check in `AGENTS.md`, with evidence, not
opinion), Open questions (empty for `READY`), Assumptions (each with its risk). Use
`Status: BLOCKED` plus the exact list of what you need from the user if any check fails.
