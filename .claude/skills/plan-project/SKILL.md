---
name: plan-project
description: Turns an idea into a runnable plan for a project cloned from this template — clarifies scope, locks a stack, drafts an approved initial STORIES.md, wires the toolchain, and picks the first vertical slice. Use on a fresh clone (placeholders still present), or when the user says "plan the project", "let's start", "kick off", "what should we build first", or describes a new app/idea from scratch.
---

# plan-project

Get from "here's my idea" to a project that's ready to build test-first — with
stories as the source of truth, not code written on a hunch. Run this once, at
the start. It does planning, **not** implementation: it produces documents and
approvals, then hands off.

## When to use

- A fresh clone of this template where `CLAUDE.md` still has `<!-- FILL IN -->`
  placeholders and `STORIES.md` still has the `CORE-*` examples.
- The user describes a new project/app/idea and wants to start.
- The user says "plan the project", "kick off", "what do we build first".

If a project is already underway (real stories, no placeholders), don't re-run
this — add or change stories through the normal approval-gated flow instead.

## Principles

- **Stories before code.** The plan's real output is an approved `STORIES.md`.
- **Approval-gated.** Every story is proposed in chat and only written after the
  owner says "approved" (see `CLAUDE.md` § "Stories workflow"). Never write
  `STORIES.md` unprompted.
- **Thin first slice.** Plan a first slice that's visible end-to-end, not a pile
  of plumbing.
- **Don't over-ask.** Batch clarifying questions; propose sensible defaults the
  user can accept rather than open-ended interrogation.

## Workflow

### 1. Understand the idea

Ask only what you can't reasonably assume. Cover, in one batched round:

- **Problem & user** — who is this for, what pain does it remove?
- **Core loop** — the one thing a user does that makes the product worth using.
- **Constraints** — the single hard constraint (privacy, offline, a platform, a
  budget, a deadline) that shapes everything else.
- **Out of scope (v1)** — what we're deliberately not doing yet.
- **"Ship-able" bar** — what has to work for a first useful release.

Play back a 3–5 sentence understanding and get a yes before proposing a stack.

### 2. Propose and lock the stack

Recommend a concrete stack (runtime, framework, language, data layer, styling,
test runner, lint/format) with versions, matched to the constraints — one
recommendation, with a one-line reason each, not a menu. On approval, fill in
the **Project-specific** sections of `CLAUDE.md`:

- Project purpose · Stack (locked) · File layout · Commands · Environment
  variables · First-time setup

Delete each `<!-- FILL IN -->` note as you complete its section.

### 3. Draft the initial stories (approval-gated)

Propose the starting `STORIES.md` **in chat** — don't write the file yet:

- Group by feature area with a stable prefix (`AUTH-`, `CORE-`, `INBOX-`, …).
- Each story is `## PREFIX-N — Title` + an "As a …, I want …, so that …"
  paragraph. Keep each one testable and about *behavior*, not implementation.
- Sequence them: what's in the first slice vs. what's on the roadmap.
- Mark roadmap stories not built yet with `<!-- @unimplemented -->`.

Iterate until the owner says "approved", then write `STORIES.md`, replacing the
`CORE-*` examples.

### 4. Sketch architecture and the first slice

Briefly (a short doc or the chat): the shape of the code (key modules/dirs, data
model, external integrations) and **the first vertical slice** — the smallest
set of stories that delivers one visible end-to-end behavior. Name the stories
it covers.

### 5. Wire the toolchain

- Point `/ship` (`.claude/commands/ship.md`) at the real typecheck / lint /
  format / test / build commands. **Keep** `node scripts/check-stories.mjs`.
- Update `.github/workflows/ci.yml` to run the same gate.
- Confirm the loop is green on the empty project:
  `node scripts/check-stories.mjs` (with `@unimplemented` markers, an
  all-roadmap `STORIES.md` passes).

### 6. Hand off

Summarize: the locked stack, the approved stories, the first slice and its story
IDs, and the immediate next step — implement the first slice **test-first**
(write the tagged test, watch it fail, make it pass). Then stop; implementation
is a separate step.

## Output checklist

- [ ] `CLAUDE.md` Project-specific sections filled, `FILL IN` notes removed.
- [ ] `STORIES.md` reflects approved stories; `CORE-*` examples gone.
- [ ] `/ship` and CI point at real commands; story-coverage step kept.
- [ ] First vertical slice identified by story ID.
- [ ] `tests/example.test.mjs` deleted once real tests exist.

## Optional: richer PM tooling

If the user's org has the `product-management` plugin (marketplace
`knowledge-work-plugins`), its `/product-management:write-spec` and
`/product-management:roadmap-update` skills pair well with steps 1–4 for a
heavier spec or a living roadmap. It's an enhancement, not a dependency — this
skill is self-contained without it.
