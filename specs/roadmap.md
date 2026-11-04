# Roadmap

> **Example content** for the made-up SignalDeck project. Step 1 replaces it.

Work one task at a time, in order. Tick a task (`[x]`) only when its "done when" has passed and
the Manager has reviewed the diff. Each task traces to requirement IDs in `mission.md`.

Rules for every phase:

- Scope is what `mission.md` lists as in scope. Nothing from "out of scope".
- Follow the constraints in `tech-stack.md`. Every endpoint gets an API test, including a
  cross-organisation test.
- A task is small enough for one worker. If it is not, the Manager splits it first.
- The full check suite (`tech-stack.md`) must be green at the end of every task.

## Phase 1: Skeleton

- [ ] **1.1** Create the Next.js + TypeScript (strict) project with pnpm, ESLint, Prettier, Vitest and
  the repo layout from `tech-stack.md`. Done when: `pnpm install` and the full check suite pass on
  a placeholder page and one trivial test.
- [ ] **1.2** Add Docker Compose (Postgres, MinIO), `.env.example`, and a typed config loader that fails
  with a readable message on a missing variable. Done when: a unit test proves the failure, and
  `docker compose up -d` starts both services.
- [ ] **1.3** Add Drizzle, the first migration (organisation, user), a `pnpm db:migrate` script and a
  database test helper. Done when: a test creates and reads an organisation.
- [ ] **1.4** Add the GitHub Actions workflow running the full check suite. Done when: the workflow file
  passes a local `act` run or the same commands pass in a clean clone.

## Phase 2: Authentication and tenancy (FR-1, FR-2, NFR-4, NFR-6)

- [ ] **2.1** Sign-up, email verification and log-in with Argon2id and cookie sessions. Done when: API
  tests cover success, wrong password, unverified email and expired token.
- [ ] **2.2** Roles (admin, editor) and invite-by-email flow. Done when: tests prove an editor cannot do
  admin-only actions and an invite can be used once.
- [ ] **2.3** Tenancy guard: every query helper takes `orgId`; add `tests/api/isolation.test.ts` that
  hits every endpoint as a second organisation. Done when: the test passes and fails if a
  query is changed to omit `orgId`.
- [ ] **2.4** Dashboard shell: log-in page, sign-up page, empty home page, log-out. Done when: a
  Playwright test signs up and lands on the home page.

## Phase 3: Media library (FR-3, NFR-5, NFR-6)

- [ ] **3.1** Media table and presigned multipart upload endpoints (start, sign part, complete, abort).
  Done when: API tests upload a fixture, abort midway, and resume with a matching checksum.
- [ ] **3.2** Type and size validation (JPG, PNG, H.264 MP4, up to 500 MB) with clear errors. Done when:
  tests reject other formats and oversize files.
- [ ] **3.3** Library page: upload with progress, thumbnails, delete. Signed download URLs expire in 1 h.
  Done when: unit test proves expiry, Playwright test uploads and deletes a fixture.

## Phase 4: Playlists and schedules (FR-4, FR-5, FR-10)

- [ ] **4.1** Playlist and item tables, API and editor page (add, reorder, set duration). Done when: API
  and Playwright tests pass.
- [ ] **4.2** Schedule logic in `src/lib/scheduling.ts`: given entries and a time, return the active
  playlist; handle overlaps and midnight ranges. Done when: unit tests cover overlap, midnight
  and no-entry cases.
- [ ] **4.3** Schedule editor UI (day of week, time range, playlist). Done when: Playwright test creates a
  schedule and the API returns it.
- [ ] **4.4** Browser preview of a playlist. Done when: Playwright test plays a two-item playlist.

## Phase 5: Pairing and player (FR-6, FR-7, NFR-1, NFR-2)

- [ ] **5.1** Pairing endpoints and dashboard "add screen" flow, following the sequence in `diagrams.md`.
  Done when: API tests cover valid code, expired code, reused code and wrong organisation.
- [ ] **5.2** Player schedule endpoint with ETag and device-secret auth. Done when: tests prove 304 on
  unchanged schedule and 401 on a wrong secret.
- [ ] **5.3** Web player: full-screen playback, service worker cache, IndexedDB schedule. Done when:
  Playwright plays a playlist full-screen.
- [ ] **5.4** Offline and update checks. Done when: the Playwright tests from the verification table for
  72 h offline (NFR-1) and 60 s update latency (NFR-2) pass.

## Phase 6: Status and logs (FR-8, FR-11)

- [ ] **6.1** Heartbeat endpoint and screen status (online, offline, never connected) with a defined
  threshold. Done when: unit tests cover the three states at the boundary.
- [ ] **6.2** Dashboard screens page showing live status. Done when: Playwright test shows a screen flip
  from online to offline.
- [ ] **6.3** Play log recording and CSV export, retained 90 days. Done when: API test exports a CSV with the
  expected rows and nothing from another organisation.

## Phase 7: Billing (FR-9)

- [ ] **7.1** Stripe subscription with per-screen quantity, checkout and cancel, in test mode. Done when:
  tests with `stripe-mock` cover create, change quantity and cancel.
- [ ] **7.2** Webhook endpoint with signature verification and idempotent handling. Done when: tests
  cover a valid event, a bad signature and a replayed event.
- [ ] **7.3** Screen limit enforced by plan, with a clear message in the dashboard. Done when: a test
  blocks pairing above the paid quantity.

## Phase 8: Hardening and release notes

- [ ] **8.1** Performance pass against NFR-3 with Lighthouse CI. Done when: the check passes or the gap
  is documented in the PR.
- [ ] **8.2** Backup and restore script and the operator guide (deploy, back up, restore). Done when: a
  restore from a backup is run once locally and the result is checked.
- [ ] **8.3** Final verification: run the whole table in `tech-stack.md`; write the PR description
  with FR/NFR evidence. Done when: every row passes or is listed as a known gap.

## Later (not scheduled)

- Native TV apps, live data widgets, audio.
- Multi-region hosting, SSO, public API.
