# Mission

> **Example content.** SignalDeck is a made-up digital signage SaaS used to show the format.
> Step 1 (see `AGENTS.md`) replaces everything in this file.

## What this is

SignalDeck is a web service that lets small businesses (cafés, gyms, clinics, shops) run the
screens on their walls from a browser. A customer uploads images and videos, arranges them into
playlists, schedules them by time of day, and pairs any screen with a cheap media player or a
smart-TV browser. The screens keep playing when the internet drops.

The first version targets owners with 1–20 screens and no IT staff. Setup must take under 10
minutes, from sign-up to content on a screen.

## Who it is for

| User | Needs |
|---|---|
| Business owner (admin) | Sign up, pay, add screens, see that screens are alive. |
| Staff editor | Upload content, edit playlists and schedules. Cannot change billing. |
| The screen (device) | Fetch its schedule, cache media, play without a person present. |

## Core principles

- **Screens never go blank.** Playback continues from cache when the network is down.
- **Ten-minute setup.** Every extra setup step must justify itself.
- **Boring and reliable over clever.** Use proven, hosted components.
- **Tenant isolation is absolute.** One customer can never see another's data or media.
- **Honest status.** The dashboard shows what a screen is actually doing, never a guess.

## Functional requirements

| ID | Requirement | Priority |
|---|---|---|
| FR-1 | A user can sign up with email and password, verify the email and log in. | Must |
| FR-2 | An organisation has an admin and can invite editors by email. | Must |
| FR-3 | A user can upload images (JPG, PNG) and videos (MP4 H.264) to a media library. | Must |
| FR-4 | A user can create a playlist from library items with a duration per item. | Must |
| FR-5 | A user can schedule a playlist for a screen by day of week and time range. | Must |
| FR-6 | An admin can pair a screen using a 6-digit code shown on the player. | Must |
| FR-7 | The player downloads its schedule and media, and plays them full-screen. | Must |
| FR-8 | The dashboard shows each screen as online, offline, or never connected. | Must |
| FR-9 | An admin can subscribe to a paid plan priced per screen, and cancel it. | Should |
| FR-10 | A user can preview a playlist in the browser before publishing. | Should |
| FR-11 | An admin can export a play log (what played on which screen, when) as CSV. | Could |

## Non-functional requirements

| ID | Requirement | Priority |
|---|---|---|
| NFR-1 | A player with a cached schedule keeps playing for at least 72 hours with no network. | Must |
| NFR-2 | A published change reaches an online screen within 60 seconds. | Must |
| NFR-3 | Dashboard pages reach interactive in under 2 s at p95 on a 4G connection. | Should |
| NFR-4 | Every database query is scoped by organisation ID, proven by an automated test. | Must |
| NFR-5 | Uploads up to 500 MB complete and resume after a dropped connection. | Should |
| NFR-6 | Passwords are hashed with Argon2id. Media URLs are signed and expire within 1 hour. | Must |
| NFR-7 | The service supports 1,000 concurrently connected screens on the chosen hosting tier. | Should |
| NFR-8 | Production data is backed up daily and a restore has been tested once. | Should |

## Deliverables

1. A web app (dashboard) and API, deployed from this repo.
2. A web-based player that runs in any modern full-screen browser.
3. Automated tests and a CI pipeline that runs them.
4. A short operator guide: how to deploy, back up and restore.

## Scope

### In scope for the first version

- Everything in the FR and NFR tables above.
- English language only. Single region hosting.

### Out of scope (for now)

- Native apps for Android TV, Tizen or webOS (the browser player covers them for now).
- Live data widgets (weather, RSS, social feeds), interactive or touch screens.
- Multi-region hosting, SSO and SAML, white-labelling, a public API.
- Audio. Content plays muted.

### Anti-goals

- A tool that needs a manual or a training call to start.
- Per-seat pricing that punishes owners for adding editors.
- Storing customer media without signed, expiring access.

## Open items to confirm

These are examples of what Step 1 must close before the specs are `READY`. See `validation.md`.

- Which payment provider and which country the company is registered in (affects tax handling).
- Hosting provider and monthly budget for storage and video bandwidth.
