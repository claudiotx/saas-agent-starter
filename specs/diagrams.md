# Diagrams

> **Example content** for the made-up SignalDeck project. Step 1 replaces it.

Text diagrams (Mermaid), so they diff and stay editable in the repo. Scope follows `mission.md`.

# System overview

```mermaid
flowchart LR
    OWNER[Owner / editor<br/>browser] --> APP[Next.js app<br/>dashboard + API]
    APP --> DB[(PostgreSQL)]
    APP --> S3[(Object storage<br/>media)]
    APP --> STRIPE[Stripe Billing]
    APP --> MAIL[Email provider]
    STRIPE -->|webhooks| APP
    OWNER -->|presigned upload| S3
    PLAYER[Screen<br/>web player] -->|poll schedule, ETag| APP
    PLAYER -->|signed media URLs| S3
    PLAYER --> CACHE[(Service worker cache<br/>+ IndexedDB)]
```

# Screen pairing (FR-6)

```mermaid
sequenceDiagram
    participant P as Player
    participant A as API
    participant U as Admin (dashboard)
    P->>A: POST /api/pair/start
    A-->>P: 6-digit code, pairing token (expires 10 min)
    P->>P: Show code on screen, poll /api/pair/status
    U->>A: Enter code, choose name
    A->>A: Bind token to organisation and new screen
    P->>A: poll /api/pair/status
    A-->>P: Paired, device secret
    P->>A: GET /api/player/schedule (device secret)
```

# Playback loop and offline behaviour (FR-7, NFR-1, NFR-2)

```mermaid
flowchart TD
    START[Player starts] --> LOAD[Load schedule from IndexedDB]
    LOAD --> PLAY[Play current item from cache]
    PLAY --> POLL{Every 30 s:<br/>network OK?}
    POLL -->|yes, ETag changed| FETCH[Download new schedule and media]
    FETCH --> SAVE[Save to IndexedDB and cache]
    SAVE --> PLAY
    POLL -->|yes, unchanged| PLAY
    POLL -->|no| MARK[Keep playing from cache<br/>mark offline]
    MARK --> PLAY
```

# Data model

```mermaid
erDiagram
    ORGANISATION ||--o{ USER : has
    ORGANISATION ||--o{ SCREEN : owns
    ORGANISATION ||--o{ MEDIA : owns
    ORGANISATION ||--o{ PLAYLIST : owns
    PLAYLIST ||--o{ PLAYLIST_ITEM : contains
    MEDIA ||--o{ PLAYLIST_ITEM : used_in
    SCREEN ||--o{ SCHEDULE_ENTRY : has
    PLAYLIST ||--o{ SCHEDULE_ENTRY : scheduled_as
    SCREEN ||--o{ HEARTBEAT : reports
```

# Build timeline

```text
Phase 1   2          3        4             5            6        7        8
Skeleton  Auth +     Media    Playlists +   Pairing +    Status + Billing  Hardening
          tenancy    library  schedules     player       logs              + release notes
```
