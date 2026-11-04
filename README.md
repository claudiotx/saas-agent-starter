# AI Project Boilerplate

A set of Markdown files that lets AI agents plan and build a project on their own. You describe
the idea. One agent turns it into validated specs. A second agent then builds it, task by task,
and opens a pull request at the end.

The `specs/` folder holds an **example project** (SignalDeck, a digital signage SaaS) so you can
see what good specs look like. Step 1 replaces it with your own.

## How it works

```text
Step 1  (new session)                     Step 2  (new session)
You + Requirements Analyst agent   ──►    Manager agent + worker subagents
  interview, write specs,                   read specs, work through roadmap.md,
  validate they are achievable              review every result, open one PR
  → specs/ + validation.md READY            → pull request to main
```

The two steps run in **separate contexts**. Start a fresh session for Step 2. The Manager
only trusts what is on disk.

## Step 1: Write and validate the specs

Start a new session in this repo and paste:

```text
You are Agent 1, the Requirements Analyst and Validator, as defined in AGENTS.md.
Read AGENTS.md and the files in specs/ (they are an example for a different project).
My project: <describe your idea in a few sentences>.
Interview me, then rewrite the specs for my project. Validate that every requirement is
clear and achievable, with no technical limits or missing dependencies, and write
specs/validation.md. Ask me anything you need. Do not start building.
```

To let the agent decide instead of asking, add: `Take assumptions where I don't know, and list them.`

Step 1 is done when `specs/validation.md` says `Status: READY` and its open-questions list is
empty. Read it, and read `specs/mission.md`. You are signing off on those.

## Step 2: Build it

Start a **new** session and paste:

```text
You are Agent 2, the Manager, as defined in AGENTS.md.
Read AGENTS.md and everything in specs/, then work through specs/roadmap.md until it is
complete. Brief worker subagents for the coding, review all their work yourself, and
work fully autonomously. Open one pull request at the end.
```

The Manager creates a feature branch, runs each roadmap task through a worker, reviews the diff
and the checks, commits, ticks the roadmap, and moves on. It makes its own decisions and
logs them in `specs/progress.md`. It does not ask you anything.

## What is in this repo

| File | Purpose |
|---|---|
| `AGENTS.md` | The rules for Agent 1, the Manager and the workers. |
| `CLAUDE.md` | Points Claude Code at `AGENTS.md`. |
| `specs/mission.md` | What and why, principles, FRs and NFRs, scope, non-goals. |
| `specs/tech-stack.md` | Decided stack, repo layout, commands, verification table. |
| `specs/data-sources.md` | External dependencies: services, keys, assets, owners. |
| `specs/diagrams.md` | Mermaid diagrams of the system and flows. |
| `specs/roadmap.md` | Ordered phases and tasks, each with a "done when". |
| `specs/validation.md` | Step 1's readiness report. The Manager refuses to start without `READY`. |
| `specs/progress.md` | The Manager's log: task results and decisions. |

## Using it for your project

1. Copy or clone this repo as the start of your project.
2. Run Step 1, then Step 2.
3. Review and merge the pull request.

To change direction later, edit the specs (or rerun Step 1 for a bigger change), then rerun Step 2.
The Manager continues from the first unticked roadmap task.

## Limits

- The agents are only as good as the specs. A vague mission gives vague results, which is why
  Step 1 is strict.
- Full autonomy means you review at the end. Keep the roadmap tasks small and the checks real,
  so the pull request is reviewable.
- This boilerplate is untested on real projects so far.
