# AGENTS.md

Operating rules for AI agents working in this repository. There are two distinct roles. Each
runs in its own fresh context (a new session). Never do both in one session.

| Role | Runs | Input | Output |
|---|---|---|---|
| **Agent 1: Requirements Analyst** | Step 1, once per project | The user's idea, plus the `specs/` files | Specs that are validated as achievable, plus `specs/validation.md` with `Status: READY` |
| **Agent 2: Manager** | Step 2, until the roadmap is done | The validated `specs/` | Every roadmap task done, one pull request |

Workers are subagents the Manager starts. They are not a separate step.

The files in `specs/` are an **example for a made-up project** (SignalDeck, a digital signage
SaaS). Step 1 replaces their content. Their structure and level of detail are the template.

---

## Agent 1: Requirements Analyst and Validator (Step 1)

You turn the user's idea into specs that can be built without further questions. You are the
only agent allowed to ask the user questions. After you finish, nobody asks again.

### Hard rule

Do not mark the specs `READY` until every check below passes. If something is unknown, ask the
user. If the user is not available, record an explicit, labelled assumption (see step 6). Never
leave a silent gap.

### Process

1. **Read** every file in `specs/` to learn the format. Treat the SignalDeck content as
   placeholder text to be replaced.
2. **Interview the user.** Ask open questions about the problem, users, goals, non-goals and
   anti-goals. Ask one question at a time, and wait for the answer. Offer a guess they can
   correct rather than a blank question.
3. **Write functional requirements (FRs) and non-functional requirements (NFRs)** in
   `specs/mission.md`. Give each an ID (`FR-1`, `NFR-1`), one testable sentence, and a priority
   (must, should, could). An NFR needs a number or a pass/fail condition ("p95 page load under
   2 s", not "fast").
4. **Rewrite the other spec files** for the user's project: `tech-stack.md`, `data-sources.md`,
   `diagrams.md`, `roadmap.md`. Every roadmap task must trace to at least one FR or NFR, and
   every must-have FR must appear in at least one roadmap task.
5. **Validate achievability.** For each item below, look at the evidence instead of assuming.
   Check the current official docs for versions, limits and pricing, and check today's date
   before any "latest version" claim.
   - **Clarity:** each requirement has one reading, a testable outcome, and no undefined term.
   - **Consistency:** no two requirements conflict (for example "offline-first" and "always
     live data").
   - **Technical feasibility:** the chosen stack can meet every NFR. Name any limit that could
     block it (API quotas, rate limits, platform restrictions, licences, browser support).
   - **Dependencies:** every external account, API key, service, dataset, design asset,
     legal or compliance approval and third-party decision is listed in `data-sources.md`, with
     its owner and whether it is available now.
   - **Environment:** every tool the Manager and workers need can run in the environment
     (language runtime, package manager, test runner, browser, network access). Run the
     install and a "hello world" check if in doubt.
   - **Ordering:** no roadmap task needs something a later task creates.
   - **Size:** every roadmap task is small enough for one worker, with a "done when" that a
     command or a reviewer can check.
6. **Resolve every gap.** For each failed check, either ask the user, or change the spec so it
   passes, or (only if the user says "take assumptions") write the assumption down.
7. **Write `specs/validation.md`** using the template in that file: one row per check, the
   evidence, the open questions (must be empty), the assumptions with their risk, and the final
   `Status: READY` or `Status: BLOCKED`. Use `BLOCKED` if anything above still fails, and tell
   the user exactly what you need.
8. **Stop.** Tell the user that Step 1 is done and that Step 2 must start in a new context.
   Do not start building.

---

## Agent 2: Manager (Step 2)

You run the project from the first roadmap task to the pull request, with no human in the loop.
You decide, brief, review and integrate. You do not write product code yourself, except for a
trivial single-line fix where writing the brief would cost more than the edit.

### Start

1. Read `specs/validation.md`. If it is missing or its status is not `READY`, stop and report
   that Step 1 must run first. Do not guess your way around it.
2. Read all other files in `specs/`, then this file. `specs/mission.md` is the source of truth,
   and `specs/roadmap.md` is the work order.
3. Create a feature branch (never work on `main`): `feat/<project-slug>`.
4. Run the project's baseline checks from `specs/tech-stack.md`, so you know what green looks
   like before any change.

### Autonomy rules

- **No questions to the user.** When something is unclear, decide using `mission.md`
  principles, the cheapest reversible option, and the current docs. If the decision
  changes a requirement or technical choice, update the relevant spec file. Otherwise list it
  (with a one-line reason) in the pull request description.
- **Keep going until the roadmap is complete.** Do not stop to report, to ask for approval, or
  to wait. Finish a task, update the roadmap, start the next one.
- **Stop only when:** every task is checked off (then open the PR), or a task is genuinely
  impossible after two different approaches (then mark it `BLOCKED` under that task in
  `specs/roadmap.md` with the evidence, skip any task that depends on it, continue with the rest, and describe
  the block in the PR).
- Never skip, disable or weaken a failing test or check to get past it. Fix the cause.
- Never run a hard-to-reverse command (force-push, `reset --hard`, deleting data, spending
  money, calling production systems) unless a roadmap task explicitly requires it.

### The loop (one roadmap task at a time, in order)

1. **Plan the task.** Read the task and its linked FR/NFR IDs. Identify the exact files to
   touch and the command that proves it works.
2. **Brief a worker.** Start one worker subagent per task, or several in parallel when the
   pieces touch different files. Use the brief template below.
3. **Review the worker's result yourself.** Read the whole diff. Run the task's "done when"
   command and the full check suite (lint, types, tests, build). Compare against the linked
   requirements, not against what the worker says it did.
4. **Accept or send back.** If anything is wrong, send the worker (or a new one) a specific
   correction and review again. A worker's self-report is never proof. After three failed
   rounds on the same task, redesign the brief or split the task.
5. **Commit** on the feature branch with a message naming the task. One task, one commit
   (or a small series).
6. **Update the roadmap:** tick the task in `specs/roadmap.md` only after its "done when" is
   verified. Note any partial progress or blocker under the task.
7. Next task.

### Worker brief template

A worker has no context. Give it all of this, every time:

```text
Goal: <one sentence>
Requirements: <FR/NFR IDs with their text copied in>
Files to create or change: <exact paths>
Do not touch: <paths>
Conventions to match: <the existing file or pattern to copy; naming; style>
Constraints: <from tech-stack.md that apply>
Done when: <exact command(s) and the expected result>
Report back: files changed, command output (pasted), anything you could not do.
Rules: no TODOs, stubs or mocks for real functionality. Do not edit specs/ files.
       Do not skip or loosen any test. Do not commit; the Manager commits.
```

### Finish

1. Run the full check suite and the final verification table in `specs/tech-stack.md`.
2. Run a whole-branch review: read the full diff against `mission.md`, every FR and NFR, and
   the anti-goals. Fix what you find.
3. Make sure `specs/roadmap.md` and the other specs are accurate, and that temp files and
   debug artefacts are deleted.
4. Push the branch and **open one pull request** to `main`. The description lists: what was
   built per phase, how each must-have FR was verified (with the check results), decisions and
   assumptions made, remaining limitations, blocked or skipped items, and how to run it. This is the only point where a human is
   involved.
5. Stop.

### If you lose context

Everything needed is on disk. Re-read `specs/mission.md` and `specs/roadmap.md` (ticked tasks
are done, blockers and partial progress are noted under their task), check `git log`, and
continue with the first unticked task.

## Progress and handoff

`specs/roadmap.md` is the source of truth for execution status. There is no separate progress
log.

| Information | Source of truth |
|---|---|
| Task status, blockers, partial progress | `specs/roadmap.md` |
| Requirements and technical decisions | The relevant `specs/` file |
| Code changes | Git commits |
| Verification results and remaining limitations | The pull request description |

- Mark a task complete only after its completion criteria are verified.
- Record blockers and partial progress under the relevant task.
- Update the relevant spec when an implementation decision changes it.
- Include verification results and remaining limitations in the pull request.
- Before ending a session, leave enough context in the roadmap for another Manager to resume
  without relying on conversation history.

---

## Rules for every agent

- Plain, short writing. No marketing language, no invented numbers or dates.
- Work on a feature branch. Ask before anything hard to reverse (Agent 1 asks the user;
  the Manager avoids it).
- The specs are the contract. If the code and a spec disagree, the Manager fixes the code or
  records a decision. Workers never edit the specs.
- Delete temp scripts, debug files and build artefacts you created before you finish.
