---
name: sdd-init
description: Customize or refresh the SDD project context for this repo — fills the scaffolded CLAUDE.md placeholders by reading the codebase, proposes extensions, and audits CLAUDE.md for drift and bloat. Invoke after `claude-sdd init`, or anytime the project context needs updating (stack changed, new top-level dirs, CLAUDE.md feels stale).
---

# SDD Init

Sibling of the `sdd` skill. Where `sdd` orchestrates the SDD pipeline for individual tasks, `sdd-init` keeps the **project-level context** (CLAUDE.md, extension selection, first ADR) accurate. Invoke this skill in two situations:

- **First-time setup** — right after `npx claude-sdd init`, when CLAUDE.md still has unresolved `{{PLACEHOLDERS}}`.
- **Refresh** — anytime the user says CLAUDE.md feels stale, or after a significant change (stack swap, monorepo split, big dependency upgrade).

The `sdd` skill itself does not do this work — if it sees unresolved `{{PLACEHOLDERS}}` it tells the user to run `/sdd-init` first.

---

## Mode A: First-time setup

Trigger: `CLAUDE.md` contains literal `{{PLACEHOLDER}}` strings (e.g. `{{PROJECT_DESCRIPTION}}`).

### Steps

1. **Read the codebase** to detect:
   - Primary language(s) and framework(s) (`package.json`, `requirements.txt`, `Cargo.toml`, `go.mod`, etc.)
   - Repo layout (monorepo, single-repo, multi-repo with submodules)
   - Test framework (`vitest.config`, `pytest.ini`, `jest.config`, etc.)
   - Linter / formatter
   - Deploy target (`Dockerfile`, `vercel.json`, `railway.toml`, `.github/workflows/`)

2. **Replace placeholders** in `CLAUDE.md`:
   - `{{PROJECT_NAME}}` → from manifest or directory name
   - `{{PROJECT_DESCRIPTION}}` → ask the user briefly if unclear
   - `{{STACK}}` → detected from above
   - `{{REPO_LAYOUT}}` → tree of top-level directories with one-line description each
   - `{{CONSTRAINTS}}` → ask the user 3-4 questions about product-specific constraints (auth provider? notification channels? compliance? performance budgets? browser support?)

3. **Detect & propose extensions.** Look at `specs/templates/feature.spec.md`:
   - Check the `<!-- Extensions enabled: ... -->` comment near the top, if any.
   - Run the heuristics below against the codebase. For each extension whose heuristic matches, propose it to the user.

   | Extension | Heuristic — propose if any are true |
   |---|---|
   | `multi-tenant` | Code or schemas reference `tenant_id`, `business_id`, `account_id`, `workspace_id`, or `org_id` as a foreign key / scope. |
   | `persistent-data` | Repo has `migrations/`, `alembic/`, `prisma/`, `drizzle/`, `schema.sql`, or an ORM dependency (sequelize, typeorm, prisma, sqlalchemy, alembic, gorm, diesel). |
   | `production-rollout` | Code references feature flags or environment-gated branching: dedicated SDKs (`launchdarkly`, `growthbook`, `unleash`, `posthog`, `flagsmith`), env-var gated `FEATURE_*`, or paired runtime modes that suggest staged rollout (e.g. an `environment` flag with values like `demo`/`real` or `sandbox`/`production`, plus a `dryRun` / `DRY_RUN` toggle used to gate writes). `dryRun` alone is not enough — there must also be an environment / mode distinction the code branches on. |
   | `operational` | Project has `Dockerfile` + observability deps (datadog, sentry, opentelemetry, prom_client) or a `runbooks/` directory. |
   | `external-deps` | Code calls third-party APIs (stripe, paddle, twilio, sendgrid) or has webhook handlers. |
   | `public-api` | Project is a library (no top-level app entry, has `main`/`exports` in `package.json`, or publishes to npm/PyPI). |

   - Show the user the proposed list with one-line rationale per match: "Propose `persistent-data` because alembic/ exists." Ask which to enable. Default = all matches.
   - For each confirmed extension, read the fragment at `specs/templates/extensions/<id>.md` and inject it into `specs/templates/feature.spec.md`. The merge:
     - **Removes** the `## Optional sections (extensions)` heading and the placeholder bullets directly under it (everything from that heading up to the next `##` heading).
     - Inserts the fragments in their place (concatenated, separated by blank lines).
     - Updates the `<!-- Extensions enabled: ... -->` comment near the top if it exists, or adds one immediately after the `> **Mode**: ...` header line if not.
   - If no extensions match, say so and skip — `feature.spec.md` stays lean.

4. If applicable, generate the first `docs/adr/0001-<area>.md` placeholder with the project context as a starting ADR.

After Mode A completes once, the project context is set. From here on, the user works through `/sdd` for tasks. They invoke `/sdd-init` again only when they want a refresh.

---

## Mode B: Refresh

Trigger: user explicitly invokes the skill on a project that no longer has `{{PLACEHOLDERS}}`. The job here is **audit + propose**, not silent rewrite.

### What to detect

Run two passes against the current state of the repo and the current `CLAUDE.md`:

#### Pass 1 — Drift (technical accuracy)

Compare what `CLAUDE.md` describes vs what the code actually shows:

- **Stack drift** — `package.json` / `pyproject.toml` / etc. have changed primary framework or major version since CLAUDE.md was written.
- **Layout drift** — top-level directories exist that are not in the Repo Layout section, or directories listed there no longer exist.
- **Test runner drift** — `vitest.config` exists but CLAUDE.md says "jest", or vice versa.
- **Deploy target drift** — `Dockerfile` / `vercel.json` / `railway.toml` appeared or disappeared.
- **Constraints drift** — if a major dependency that materially changes constraints was added (e.g. `stripe` first appeared → billing constraint should be documented; `prisma` first appeared → DB schema constraint).

#### Pass 2 — Bloat (does the agent benefit?)

Flag sections of `CLAUDE.md` that an agent does not use. Be **conservative** — false positives erode trust. Only flag with high confidence:

| Pattern | Why flag |
|---|---|
| Section is ≥80% identical to a section in `README.md` | Duplication; reference README instead. |
| Section titled `Setup`, `Installation`, `Getting started`, `Development environment` | Typically human onboarding; agents do not run setup. |
| Multi-step procedural tutorials for operational actions | Belongs in `docs/runbooks/`. |
| Sections titled `History`, `Decisions made`, `Changelog`, `Past versions` | Belongs in `docs/adr/`. |
| Bullet lists of generic best practices without project-specific qualifiers (e.g. "write tests", "follow DRY", "use semantic commits", "prefer composition over inheritance" — the LLM already knows these) | Pure noise. |

**Do NOT flag** as bloat:
- Project-specific conventions ("we use snake_case for DB columns and camelCase for API responses").
- Domain glossary / terminology specific to the product.
- Constraints, tradeoffs, "why we don't do X" notes.
- Repo layout, even if long.

When in doubt, keep it.

### What to report

Present findings to the user in this format, then ask which option they want:

```
🔧 Drift detected:
  - Stack: <what changed>
  - Layout: <what changed>
  - <other category>: <what changed>

🪓 Possibly unnecessary content (<N> lines flagged):
  - "## <section>" (<L> lines) — <reason flagged>
  - "## <section>" (<L> lines) — <reason flagged>

How should I proceed?
  A) Full improvement — apply drift fixes AND remove the flagged bloat.
  B) Drift only — fix what's wrong/missing, leave existing content alone.
  C) Pick per item — I'll show you each suggestion and you accept/reject one by one.
```

**Default recommendation when the user is silent: B.** Conservative, respects the user's writing, only fixes what's factually wrong.

### How to apply

After the user picks:

- **Use `Edit` for targeted changes**, never write the whole file.
- For drift fixes: replace the specific stale string (e.g. update `**Stack**: Vite + React` → `**Stack**: Next.js 15`).
- For bloat removal: replace the entire section (heading + body) with the empty string, or with a short reference (e.g. "Setup steps live in [README.md](./README.md#setup).").
- **Show the diff before each edit** for option A and C; for option B (drift only), the changes are mechanical and can be batched, but still summarize what was changed at the end.
- **Never** invent content. If a section needs more detail than the codebase reveals, ask the user.

### Out of scope for refresh

- Re-running the constraints questionnaire from Mode A (those answers are user-supplied; do not regenerate without the user asking).
- Touching `templates/CLAUDE.md` in the `claude-sdd` repo. This skill operates on the user's project's CLAUDE.md only.
- Changing `specs/features/<area>/*.spec.md` — those are owned by the SDD pipeline (`sdd` skill).

---

## What `sdd-init` is NOT

- Not a task classifier — that's `sdd`.
- Not a spec generator — that's `sdd` + the prompts in `specs/prompts/`.
- Not a project bootstrapper — that's `npx claude-sdd init`. This skill runs *after* the bootstrapper.
