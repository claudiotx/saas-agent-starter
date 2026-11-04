# Spec-Driven AI Development

**An AGENTS.md-based development template that takes a project from requirements to a reviewed pull request.**

Define the project before delegating the implementation.

This template gives AI coding agents a structured workflow: clarify requirements, assess
implementation readiness, then execute an ordered roadmap through manager–worker orchestration.

The workflow separates planning from execution. Requirements are approved before coding
begins. Implementation proceeds against written specifications, explicit completion criteria,
and verification checks—not conversation history.

**Designed for small software products, SaaS MVPs, and bounded development projects.**

> **Project status:** This template has not yet been tested on real projects.
> It defines a development workflow; it does not guarantee implementation quality,
> security, or production readiness.

## The method

Two stages. Separate sessions. One explicit handoff.

```text
1. Specify and validate                  2. Implement and review
   You + Requirements Analyst               Manager + worker subagents

   Clarify the product                       Read the approved specifications
   Define scope and constraints              Execute the ordered roadmap
   Resolve dependencies                      Review each worker's output
   Establish acceptance criteria             Run verification checks
   Assess implementation readiness           Record status in the roadmap

   → specs/ + validation.md READY       → One pull request for human review
```

The second stage starts in a fresh session. The Manager treats the files on disk
as the source of truth, rather than relying on the planning conversation.

## Operating principles

- **Specify before implementing.** Define requirements, scope, dependencies, and
  completion criteria before delegating coding.
- **Gate execution on readiness.** The Manager does not start unless
  `specs/validation.md` reports `Status: READY`.
- **Separate execution from review.** Workers implement individual tasks.
  The Manager reviews their changes and verification results.
- **Make decisions traceable.** Task status lives in `specs/roadmap.md`, decisions in
  the relevant spec, changes in Git, and verification results in the pull request.
- **Keep human approval explicit.** You approve the specifications before
  implementation and review the pull request before merging.

`READY` means the specifications meet the workflow's readiness criteria.
It is not proof that every technical assumption is correct.

## Quick start

1. Copy this repository or use it as a GitHub template.
2. Start a new agent session and run **Stage 1** below.
3. Review and approve the resulting specifications.
4. Start a fresh agent session and run **Stage 2**.
5. Review the pull request before merging.

The `specs/` directory contains a worked specification example:
**SignalDeck**, a digital signage SaaS. Stage 1 replaces these files with
specifications for your project.

## Stage 1 — Specify and validate

Start a new session in this repository and paste:

```text
You are Agent 1, the Requirements Analyst and Validator, as defined in AGENTS.md.

Read AGENTS.md and the files in specs/. The existing specifications are an
example for a different project.

My project: <describe your idea in a few sentences>.

Interview me, then rewrite the specifications for my project.

Define scope, requirements, technical constraints, external dependencies,
acceptance criteria, and an ordered implementation roadmap.

Assess whether the project is ready for implementation. Identify unresolved
questions, unsupported assumptions, feasibility risks, and missing dependencies.
Write your findings in specs/validation.md.

Ask me anything you need. Do not start building.
```

If you want the agent to propose defaults, add:

```text
Where I do not have a preference, propose a reasonable assumption and record it.
Do not treat an unverified assumption as a verified fact.
```

### Approval gate

Stage 1 is complete when:

- `specs/validation.md` reports `Status: READY`.
- Its open-questions list is empty.
- You have reviewed the readiness report, mission, scope, and roadmap.
- You accept the recorded assumptions and constraints.

You are approving the implementation brief—not certifying that the generated
specifications are error-free.

## Stage 2 — Implement and review

Start a **fresh session** and paste:

```text
You are Agent 2, the Manager, as defined in AGENTS.md.

Read AGENTS.md and everything in specs/. Confirm that specs/validation.md
reports Status: READY.

Work through specs/roadmap.md in order. Brief worker subagents for implementation,
review their changes yourself, and run the required verification checks.

Make implementation decisions within the approved scope. Update the relevant spec
when a decision changes it. Do not claim a task is complete without evidence that its
completion criteria have been met.

Work autonomously as defined in AGENTS.md. Open one pull request at the end,
summarizing the implementation, verification results, and any remaining limitations.
```

The Manager:

1. Creates a feature branch.
2. Delegates each roadmap task to a worker.
3. Reviews the diff and verification results.
4. Commits accepted work and marks completed tasks.
5. Notes blockers and partial progress under the task in `specs/roadmap.md`.
6. Opens one pull request for human review.

The Manager operates without routine clarification questions during this stage.
Resolve product decisions during Stage 1 so implementation has a clear brief.

## Repository structure

| File | Responsibility |
|---|---|
| `AGENTS.md` | Roles, operating rules, and handoffs for the Analyst, Manager, and workers. |
| `CLAUDE.md` | Directs Claude Code to `AGENTS.md`. |
| `specs/mission.md` | Product purpose, principles, functional and non-functional requirements, scope, and non-goals. |
| `specs/tech-stack.md` | Selected stack, repository layout, commands, and verification table. |
| `specs/data-sources.md` | External services, credentials, assets, dependencies, and owners. |
| `specs/diagrams.md` | Mermaid diagrams describing system structure and flows. |
| `specs/roadmap.md` | Ordered implementation phases and tasks with explicit completion criteria. |
| `specs/validation.md` | Readiness assessment and the gate between planning and implementation. |

## Changing direction

For a small change, update the affected specifications and roadmap.

For a material change in scope or architecture, rerun Stage 1 and review the
updated readiness assessment before resuming implementation.

The Manager resumes from the first unchecked roadmap task. If a change invalidates
previously completed work, update the roadmap to include the required rework.

## What this template is—and is not

This is a **Markdown-first workflow template for AI-assisted development**.

It is not:

- A prebuilt SaaS application.
- An agent runtime or orchestration framework.
- A replacement for engineering judgment or human review.
- A guarantee of secure, correct, or production-ready software.

Your agent environment must support the operations required by the workflow,
including subagents, file editing, command execution, Git, and pull-request creation.

## Limitations

- **Not yet field-tested.** Real-project reliability has not been established.
- **Specification quality matters.** Missing constraints or weak acceptance criteria
  can produce an implementation that passes checks but misses the product need.
- **Agent validation is fallible.** Feasibility assessments and dependency checks
  may require independent verification.
- **Autonomy needs boundaries.** Keep scope bounded, tasks small, and verification
  checks meaningful.
- **Review remains necessary.** Review the specifications before execution and the
  implementation before merging. Apply additional security and deployment checks
  appropriate to your project.
