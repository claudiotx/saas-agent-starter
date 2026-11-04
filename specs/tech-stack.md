# Tech Stack

> **Example content** for the made-up SignalDeck project. Step 1 replaces it.
> Versions below are placeholders. Agent 1 must check the current official docs and today's
> date, then write the real versions here.

Constraints that apply to every task. Scope follows `mission.md`, flows are in `diagrams.md`,
and external dependencies are in `data-sources.md`.

## Decided stack

| Concern | Choice | Why |
|---|---|---|
| Language | TypeScript (strict), Node LTS | One language across dashboard, API and player. |
| Dashboard and API | Next.js (App Router) with route handlers | One deployable, server rendering, simple auth. |
| Database | PostgreSQL | Relational data, row-level scoping by organisation. |
| ORM and migrations | Drizzle ORM, SQL migrations committed | Typed queries, reviewable migrations. |
| Auth | Email and password, Argon2id, session cookies (HttpOnly, Secure, SameSite=Lax) | NFR-6. |
| Media storage | S3-compatible object storage, presigned multipart uploads and signed downloads | NFR-5, NFR-6. |
| Video handling | Accept H.264 MP4 only in v1. No transcoding. | Keeps scope small. Rejected formats show a clear error. |
| Player | Static web page (`/player`), service worker for cache, IndexedDB for schedule | NFR-1. |
| Realtime updates | Player polls the schedule endpoint every 30 s with an ETag | NFR-2. Simpler than websockets. |
| Payments | Stripe Billing, per-screen quantity subscription, webhooks | FR-9. |
| Email | Transactional email provider (verification, invites) | FR-1, FR-2. |
| Package manager | pnpm, committed lock file | Reproducible installs. |
| Lint, format, types | ESLint, Prettier, `tsc --noEmit` | Same checks locally and in CI. |
| Tests | Vitest (unit, API), Playwright (end-to-end, player offline test) | Verification table below. |
| CI | GitHub Actions | Runs the check suite on every push. |

## Repository layout

```text
signaldeck/
  AGENTS.md  README.md  CLAUDE.md
  specs/
  src/
    app/            dashboard pages and API route handlers
    db/             Drizzle schema, migrations, query helpers (all take orgId)
    lib/            auth, storage, billing, scheduling logic (pure, unit-tested)
    player/         the web player
  tests/
    unit/  api/  e2e/
  .github/workflows/ci.yml
```

## Commands

| Task | Command |
|---|---|
| Install | `pnpm install` |
| Dev server | `pnpm dev` |
| Lint | `pnpm lint` |
| Type check | `pnpm typecheck` |
| Unit and API tests | `pnpm test` |
| End-to-end tests | `pnpm test:e2e` |
| Build | `pnpm build` |
| Full check suite | `pnpm lint && pnpm typecheck && pnpm test && pnpm build` |

## Environment

- Local Postgres through Docker Compose (`docker compose up -d db`).
- Object storage in development: a local S3-compatible container (for example MinIO).
- All secrets come from environment variables. `.env.example` lists every name with no values.
  Real `.env` files are never committed.
- Third-party services (Stripe, email) use test mode in development and CI.

## Constraints for every task

- Every database function takes an `orgId` and filters by it (NFR-4).
- No copy-pasted business logic between dashboard and player: shared logic lives in `src/lib`.
- No secrets, keys or customer data in logs.
- Every new endpoint has an API test, including one that proves another organisation cannot
  reach it.

## Verification table

Run at the end of the project (and by the Manager after each phase where it applies).

| Check | How | Pass condition |
|---|---|---|
| Lint, types, unit, API tests, build | Full check suite | All pass, no skipped tests |
| Tenant isolation (NFR-4) | `tests/api/isolation.test.ts` runs every endpoint as a second org | No endpoint returns the first org's data |
| Offline playback (NFR-1) | Playwright: load player, go offline, advance the clock 72 h | Player keeps playing from cache |
| Update latency (NFR-2) | Playwright: publish a change, time the player | Visible within 60 s |
| Upload resume (NFR-5) | API test: abort a multipart upload midway, resume | File completes, checksum matches |
| Password and URL security (NFR-6) | Unit tests | Argon2id used; signed URL expires after 1 h |
| Page speed (NFR-3) | Lighthouse CI, simulated 4G | Interactive under 2 s |
