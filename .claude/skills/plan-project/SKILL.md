---
name: plan-project
description: First-run kickoff for a project cloned from this template. Orchestrates the bundled superpowers planning skills (brainstorming → writing-plans) and adds the template-specific glue — an approval-gated STORIES.md, filled-in CLAUDE.md, and wired toolchain. Use on a fresh clone (placeholders still present), or when the user says "plan the project", "let's start", "kick off", "what should we build first", or describes a new app/idea from scratch.
---

# plan-project

Take an idea to a project that's ready to build test-first. This skill is a thin
**orchestrator**: the thinking is done by two battle-tested skills bundled from
the [`obra/superpowers`](https://github.com/obra/superpowers) collection —

- **`brainstorming`** — explore intent, constraints, and design; ends in an
  approved design spec. (It hard-gates: no code until you've presented a design
  and the user approved it.)
- **`writing-plans`** — turn a spec into a bite-sized, test-first implementation
  plan.

What this skill adds is the glue those generic skills don't know about: this
template's **STORIES.md** as the approval-gated source of truth, the
**Project-specific** sections of `CLAUDE.md`, and the `/ship` + CI wiring.

## When to use

- A fresh clone where `CLAUDE.md` still has `<!-- FILL IN -->` placeholders and
  `STORIES.md` still has the `CORE-*` examples.
- The user describes a new project and wants to start.

If the project is already underway (real stories, no placeholders), don't re-run
this — add or change stories through the normal approval-gated flow, and reach
for `brainstorming` / `writing-plans` per-feature instead.

## Workflow

### 1. Brainstorm the design — invoke `brainstorming`

Hand off to the `brainstorming` skill. Let it drive: explore context, ask
questions one at a time, propose approaches, present a design, and — on approval
— write the design spec (it saves to `docs/superpowers/specs/`). Don't shortcut
its hard gate; come back here only once the user has approved the design.

### 2. Lock the stack → fill in `CLAUDE.md`

From the approved design, recommend a concrete stack (runtime, framework,
language, data layer, styling, test runner, lint/format) with versions — one
recommendation with a one-line reason each. On approval, fill the
**Project-specific** sections of `CLAUDE.md` (purpose, stack, layout, commands,
env vars, setup) and delete each `<!-- FILL IN -->` note.

### 3. Derive the stories → `STORIES.md` (approval-gated)

Translate the approved design into behavioral stories. **Stories are the
template's source of truth for product behavior** — a different artifact from the
superpowers design spec (which captures *how* it's built). Propose the story text
**in chat** first:

- Group by feature area with a stable prefix (`AUTH-`, `CORE-`, …); each story is
  `## PREFIX-N — Title` + an "As a …, I want …, so that …" paragraph about
  *behavior*, not implementation.
- Sequence them: first-slice stories vs. roadmap. Mark not-yet-built ones
  `<!-- @unimplemented -->`.

Iterate until the owner says "approved", then write `STORIES.md`, replacing the
`CORE-*` examples.

### 4. Wire the toolchain

Point `/ship` (`.claude/commands/ship.md`) and `.github/workflows/ci.yml` at the
stack's real typecheck / lint / format / test / build commands. **Keep**
`node scripts/check-stories.mjs`. Confirm the gate is green on the empty project
(an all-`@unimplemented` `STORIES.md` passes).

### 5. Plan the first slice — invoke `writing-plans`

Pick the smallest vertical slice that delivers one visible end-to-end behavior;
name the story IDs it covers. Hand off to `writing-plans` to produce the
implementation plan for that slice (bite-sized, test-first tasks).

### 6. Hand off to implementation

Implement the first slice **test-first**, per this template's workflow: write the
`@story:`-tagged test, watch it fail, make it pass, run `/ship`. Delete
`tests/example.test.mjs` once real tests exist. Then stop — implementation is a
separate step.

> `writing-plans` offers a superpowers execution hand-off
> (`subagent-driven-development` / `executing-plans`). Both are bundled, but
> this template's own test-first + `/ship` loop is enough for a first slice —
> reach for them when a plan is big enough to need checkpoints or parallelism.

## Output checklist

- [ ] Design spec written & approved (via `brainstorming`).
- [ ] `CLAUDE.md` Project-specific sections filled, `FILL IN` notes removed.
- [ ] `STORIES.md` reflects approved stories; `CORE-*` examples gone.
- [ ] `/ship` and CI point at real commands; story-coverage step kept.
- [ ] First slice planned (via `writing-plans`), identified by story ID.
- [ ] `tests/example.test.mjs` deleted once real tests exist.
