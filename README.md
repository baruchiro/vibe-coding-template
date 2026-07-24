# vibe-coding-template

A starting point for AI-driven ("vibe coding") projects. It ships the
**process** that makes a repo safe to hand to an agent end-to-end — not a stack.
Drop your framework of choice on top and keep the workflow.

Distilled from [`baruchiro/github-management`](https://github.com/baruchiro/github-management).

## What's in the box

| File | Purpose |
|---|---|
| `CLAUDE.md` | The agent's guide. Portable "how we work" rules + placeholders for your stack. |
| `STORIES.md` | Source of truth for product behavior. Approval-gated, coverage-enforced. |
| `scripts/check-stories.mjs` | Fails the build if a story has no tagged test (or a test tags an unknown story). Stack-agnostic. |
| `tests/example.test.mjs` | A working example test tagged `// @story: CORE-1`, so the loop is green from the first commit. |
| `.claude/skills/plan-project/SKILL.md` | `/plan-project` — the first-run kickoff: idea → locked stack → approved stories → first slice. |
| `.claude/commands/ship.md` | `/ship` — the pre-push gate (typecheck, lint, story-coverage, tests, build). |
| `.claude/skills/open-pr/SKILL.md` | `/open-pr` — run the gate, push, open the PR. |
| `.github/workflows/ci.yml` | CI. The story-coverage job runs as-is; the rest is a placeholder for your stack. |

## The workflow in one paragraph

Behavior is written down as **stories** in `STORIES.md` before it's built, and
every story change needs the owner's approval. Each story earns at least one
test tagged `// @story: <ID>`; a coverage gate fails the build if any story is
untested or a test references a story that doesn't exist. Before pushing, `/ship`
runs the whole gate. PRs go out through `/open-pr`. Picking up a GitHub issue has
a fixed opening (assign → comment session link → branch `{issue}-{slug}` → PR
that `Closes #N`). It's all spelled out in `CLAUDE.md`.

## Starting a new project from this template

1. Copy these files into your new repo (or use it as a GitHub template repo).
2. **Kick off with the agent**: describe your idea and run `/plan-project`. It
   walks the empty-project bootstrap — clarify scope, lock a stack, draft
   approved stories, wire the toolchain, pick the first slice. `CLAUDE.md`'s
   "First run" section tells the agent to do this before writing any code.

   Doing it by hand instead? Steps 3–6 are that flow spelled out:
3. Fill in every `<!-- FILL IN -->` section of `CLAUDE.md` — stack, commands,
   layout, env vars, setup. Delete the notes as you go.
4. Point the `/ship` command and `.github/workflows/ci.yml` at your real
   commands. Keep the `node scripts/check-stories.mjs` step.
5. Replace the two `CORE-*` example stories in `STORIES.md` with your own (get
   them approved first), and replace `tests/example.test.mjs` with real tests.
6. Verify the loop is green:
   ```sh
   node scripts/check-stories.mjs
   node --test           # or your test runner
   ```

## The story-coverage gate

```sh
node scripts/check-stories.mjs
```

It scans common source dirs for test files and reads their `@story:` comment
headers. Override where it looks with env vars if your layout is unusual:

- `STORY_TEST_ROOTS` — comma-separated dirs to scan (default: `src,app,lib,test,tests,e2e,spec,packages`, whichever exist).
- `STORY_TEST_GLOB` — a JS RegExp source string for test filenames (default covers `.test.`/`.spec.`/`_test.`/`test_*.py` across common languages).
