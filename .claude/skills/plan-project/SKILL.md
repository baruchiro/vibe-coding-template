---
name: plan-project
description: Low-friction first-run kickoff for a project cloned from this template. The agent does the legwork and prepares one compact kickoff proposal; the owner approves the few decisions that matter (implementation form, design, initial stories). Adds the template-specific glue — an approval-gated STORIES.md, filled-in CLAUDE.md, and wired toolchain. Use on a fresh clone (placeholders still present), or when the user says "plan the project", "let's start", "kick off", "what should we build first", or describes a new app/idea from scratch.
---

# plan-project

Take an idea to a project that's ready to build test-first — **without putting
the owner through an interview**. This template's working style is
assistant-led: you do the research, reading, and drafting; the owner makes the
few decisions that are actually theirs. Never run a multi-round question
series. When something is genuinely unclear, ask the smallest set of questions
that settles it — one round, each question with your recommended answer.

Three decisions belong to the owner; everything else is preparation you do
without asking. Prepare all three and present them as **one compact kickoff
proposal** — the owner's single approval of it covers all three, so there are
no separate approval round-trips later in this flow:

1. **Implementation form** — skill with scripts, local MCP, a combination, or
   an application (the gate in step 2), with the recommended stack.
2. **Design** — the short design that `brainstorming` would produce.
3. **Initial stories** — the behavior contract in `STORIES.md`
   (approval-gated on behavior; see step 4).

This skill is a thin **orchestrator**: the thinking is done by battle-tested
skills vendored from elsewhere —

- **`brainstorming`** — from [`obra/superpowers`](https://github.com/obra/superpowers).
  Explore intent, constraints, and design; ends in an approved design spec. (It
  hard-gates: no code until you've presented a design and the user approved it.)
  Inside this flow it is scoped to the batched kickoff (step 2): its
  one-at-a-time questioning and per-section approvals are replaced by one
  question round and one combined approval, while its hard gate stays.
- **`writing-plans`** — from `obra/superpowers`. Turn a spec into a bite-sized,
  test-first implementation plan.

The vendored `grill-me`/`grilling` skill (a relentless interview) is **not part
of this flow** — invoke it only when the user explicitly asks to be grilled.

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

### 1. Clarify — only what's blocking

If the idea arrives loose ("something like X" with no real scope yet), do the
legwork first: research the domain, look at the owner's existing work, and form
a concrete proposal of your own. Then ask **only** the questions that block
the design — one short round (it may hold several questions), each with a
recommended answer so the owner can reply "yes" or adjust. Facts are yours to
find, never the owner's to supply; decisions are theirs. If the answers raise a
further decision, don't open another round: fold it into the kickoff proposal
as a flagged recommendation the owner can adjust. If the idea already arrives
scoped, skip to step 2 with no questions.

### 2. Brainstorm the design and present the kickoff proposal — invoke `brainstorming`

Hand off to the `brainstorming` skill, scoped to this flow. It supplies the
context exploration, the comparison of approaches, and the design spec; this
skill overrides its pacing:

- Skip its one-at-a-time questioning — step 1 already settled the questions.
- Apply the implementation-form gate below while comparing approaches.
- Don't ask for approval section by section. Draft the design, the recommended
  stack (step 3) and the initial stories (step 4), and present them together as
  **one kickoff proposal** in chat.
- That proposal is brainstorming's hard gate: do not scaffold, install, or write
  code until the owner approves it. A one-word "yes" approves all three
  decisions; if the owner adjusts one, revise that part and reconfirm only it.
- On approval, write the design spec (it saves to `docs/superpowers/specs/`)
  mirroring the approved proposal and run its inline self-review. Don't wait for
  a separate spec-review round: point the owner to the spec path in the final
  hand-off.

#### Required gate: choose the implementation form before the stack

Once the problem, users and acceptance criteria are clear enough, compare a
skill with local scripts, a local MCP server, a combination, and an application.
Do this **inside brainstorming, before asking for design approval**; do not
approve a full-stack design and only then ask whether it was needed. If the
initial idea is already scoped, run this check immediately. If the answer is
unclear, clarify the missing requirement instead of scaffolding a default app.

Ask whether the required outcome can be delivered without a deployed server or
a custom UI. Recommend the smallest sufficient form, identify any requirements
it cannot meet, and include it in the kickoff proposal; the owner's approval of
that proposal is the explicit approval required before locking a stack,
scaffolding, or installing infrastructure. Record this decision in the design
spec and the Project-specific section of `CLAUDE.md`.

Distinguish workflow guidance from a tool protocol: a skill can run scripts and
call existing remote APIs; a local MCP server can do the same, or operate purely
on local data via `stdio`. MCP earns its extra packaging and lifecycle only when
requirements need its standardized, discoverable tool interface (for example,
reuse by multiple MCP clients). A remote API call alone is not a reason to add
MCP, and does not require deploying a new server. A skill plus MCP is also valid
when the workflow and reusable tool interface serve distinct needs.

If an application is necessary, name the specific requirements that need a
new remote service and those that need a custom UI **separately**. Consider the
agent's existing interface before adding one. Revisit the decision if subsequent
requirements introduce unmet needs; get approval before expanding the form.

### 3. Lock the minimal toolchain → fill in `CLAUDE.md`

From the design and implementation form, recommend only the needed
stack components (runtime, framework, language, data layer, styling, test runner,
lint/format) with versions — one
recommendation with a one-line reason each — as part of the kickoff proposal.
Its approval covers the stack; don't ask again. Once approved, fill the
**Project-specific** sections of `CLAUDE.md` (purpose, stack, layout, commands,
env vars, setup) and delete each `<!-- FILL IN -->` note. Mark unneeded components
and commands as not applicable with a reason; do not add a framework, database,
dev server or build process just to fill a placeholder. For skills and MCP,
document installation/client integration and an actual invocation instead of
assuming a browser URL.

### 4. Derive the stories → `STORIES.md` (approval-gated)

Translate the design into behavioral stories. **Stories are the
template's source of truth for product behavior** — a different artifact from the
superpowers design spec (which captures *how* it's built). Include the story text
in the kickoff proposal, **in chat**, before writing the file:

- Group by feature area with a stable prefix (`AUTH-`, `CORE-`, …); each story is
  `## PREFIX-N — Title` + an "As a …, I want …, so that …" paragraph about
  *behavior*, not implementation.
- Sequence them: first-slice stories vs. roadmap. Mark not-yet-built ones
  `<!-- @unimplemented -->`.

The owner's approval of the kickoff proposal approves these stories; revise
only the parts they adjusted. Then write `STORIES.md`, replacing the `CORE-*`
examples. Later changes to what a story says the product does need fresh
approval, per `CLAUDE.md`.

### 5. Wire the toolchain

Point `/ship` (`.claude/commands/ship.md`) and `.github/workflows/ci.yml` at the
stack's real typecheck / lint / format / test / build commands. **Keep**
`node scripts/check-stories.mjs`. Confirm the gate is green on the empty project
(an all-`@unimplemented` `STORIES.md` passes). For non-application forms, keep
only meaningful gate commands and document why any stage is not applicable.
Keep executable-helper tests and story coverage; pure instruction skills need
representative agent/workflow verification rather than a fake app test. Exercise
scripts directly, or invoke tools through a real MCP client for MCP behavior.

#### Browser verification — install `playwright-cli`

**If the stack has a UI**, set up `playwright-cli` so the agent can actually
drive the app, as `CLAUDE.md`'s
[Verifying your change](../../../CLAUDE.md#verifying-your-change) requires.
Prefer it over the Playwright MCP: it's the
[Playwright CLI for coding agents](https://playwright.dev/docs/getting-started-cli),
and CLI commands keep large tool schemas and verbose accessibility trees out of
the context window. Skip this whole sub-step for a backend-only stack and say so
rather than installing it unused.

The **skill** is already vendored at `.claude/skills/playwright-cli/`. What a
fresh clone needs is the **binary**:

```sh
npm install -g @playwright/cli@latest   # any language
playwright-cli --help
```

If the stack already depends on Playwright, use the bundled entry point instead
of a global install — `npx playwright cli <command>` (JS/TS) or
`python -m playwright cli <command>` (Python). Substitute that for
`playwright-cli` in every command below.

Then make the browser reachable and prove it works against the real dev server:

```sh
playwright-cli install-browser chromium
playwright-cli open http://localhost:<dev-port> --headed
playwright-cli resize 375 667                     # mobile-first, per CLAUDE.md
playwright-cli snapshot
playwright-cli close
```

To refresh the vendored skill from the installed CLI (it ships with the CLI, so
it moves with the version):

```sh
playwright-cli install --skills          # → .claude/skills/playwright-cli/
playwright-cli install --skills -g       # instead share it across all projects
playwright-cli install --skills=agents   # AGENTS.md-style agents instead of claude
```

**Sandboxed environments** (Claude Code on the web, CI images, most containers)
often ship a browser already and block the download CDN, and run as root so
Chromium's sandbox fails. `install --skills` still succeeds there — it's
`install-browser` that fails, and the two are independent. Point the CLI at the
existing binary via `.playwright/cli.config.json`, which it auto-loads:

```json
{
  "browser": {
    "launchOptions": {
      "executablePath": "/opt/pw-browsers/chromium",
      "chromiumSandbox": false
    }
  }
}
```

It resolves relative to the working directory, so run `playwright-cli` from the
repo root or it silently falls back to downloading a browser. Commit the config
when the whole team shares one container image; leave it uncommitted when the
path is a local quirk — a wrong `executablePath` fails the launch outright
instead of falling back. `.gitignore` already ignores everything else under
`.playwright/`, plus `.playwright-cli/` (snapshot and screenshot output).

### 6. Plan the first slice — invoke `writing-plans`

Pick the smallest vertical slice that delivers one visible end-to-end behavior;
name the story IDs it covers. Hand off to `writing-plans` to produce the
implementation plan for that slice (bite-sized, test-first tasks).

### 7. Hand off to implementation

Implement the first slice **test-first**, per this template's workflow: write the
`@story:`-tagged test, watch it fail, make it pass, run `/ship`. Delete
`tests/example.test.mjs` once real tests exist. Then stop — implementation is a
separate step.

> `writing-plans` offers a superpowers execution hand-off
> (`subagent-driven-development` / `executing-plans`). Both are bundled, but
> this template's own test-first + `/ship` loop is enough for a first slice —
> reach for them when a plan is big enough to need checkpoints or parallelism.

## Output checklist

- [ ] Blocking questions asked in at most one short round, each with a
      recommended answer (no interview; `grill-me` only if the user asked).
- [ ] One kickoff proposal (implementation form, design, stack, initial stories)
      presented and approved before any scaffolding or code.
- [ ] Design spec written from the approved proposal (via `brainstorming`).
- [ ] Implementation form compared and approved as part of that proposal;
      decision recorded in the design spec and `CLAUDE.md`.
- [ ] Remote server and custom UI each justified or explicitly unnecessary;
      MCP's protocol layer justified if chosen over scripts alone.
- [ ] `CLAUDE.md` Project-specific sections filled, `FILL IN` notes removed.
- [ ] `STORIES.md` reflects approved stories; `CORE-*` examples gone.
- [ ] `/ship` and CI point at real commands; story-coverage step kept.
- [ ] UI stack: `playwright-cli` installed and verified against the dev server
      (or explicitly skipped as backend-only).
- [ ] First slice planned (via `writing-plans`), identified by story ID.
- [ ] `tests/example.test.mjs` deleted once real tests exist.
