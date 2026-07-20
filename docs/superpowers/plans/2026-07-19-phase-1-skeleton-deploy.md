# Phase 1 — Skeleton & Deploy Path Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [x]`) syntax for tracking.

**Goal:** An Astro site scaffolded at `apps/site/` with one placeholder page, deploying automatically to `kylekubik.dev` on every push to `main` via a Render Blueprint.

**Architecture:** Public monorepo (`kylekubik.dev` on GitHub). The Astro static site is the only app for now, at `apps/site/`; `render.yaml` at repo root defines the static site service with `rootDir` + `buildFilter` so future `apps/<name>` services never couple to this build. DNS via Porkbun points the apex domain at Render.

**Tech Stack:** Astro (latest, minimal template, zero integrations), Render static site, GitHub, Porkbun DNS.

## Global Constraints

- Minimize dependencies: no UI libraries, no CSS frameworks, no React in this phase (PRD §2).
- No `localStorage`/browser-storage patterns in the core site (PRD §2).
- Everything reproducible via `render.yaml` — no manual dashboard-only config beyond the one-time Blueprint connect (PRD §2).
- Repo layout per `docs/superpowers/specs/2026-07-19-repo-structure-design.md` — site lives at `apps/site/`, never at repo root.
- Success for this phase (PRD §8): a push to `main` results in an automatic deploy visible at `kylekubik.dev`.

---

### Task 1: Repo hygiene — branch, .gitignore

**Files:**
- Create: `.gitignore`

**Interfaces:**
- Produces: `main` as the default branch (GitHub push in Task 6 depends on it).

- [x] **Step 1: Rename branch to main**

Run: `git branch -M main`
Expected: `git branch` shows `* main`.

- [x] **Step 2: Create .gitignore**

```gitignore
# dependencies
node_modules/

# build output
dist/
.astro/

# environment
.env
.env.*
!.env.example

# OS / editor
.DS_Store
Thumbs.db

# logs
npm-debug.log*
```

- [x] **Step 3: Commit**

```bash
git add .gitignore
git commit -m "chore: add .gitignore, rename branch to main"
```

---

### Task 2: README with subdomain-app playbook

**Files:**
- Create: `README.md`

**Interfaces:**
- Consumes: repo structure from the approved spec.
- Produces: the documented "add a subdomain app" pattern required by PRD §7.

- [x] **Step 1: Write README.md**

```markdown
# kylekubik.dev

Personal portfolio, blog, and project ecosystem. Live at [kylekubik.dev](https://kylekubik.dev).

## Layout

| Path | What it is |
|---|---|
| `apps/site/` | The main Astro site (static, deployed to kylekubik.dev) |
| `docs/` | PRD, stack decisions, specs, and plans |
| `docs/design/` | Design system spec + reference HTML (Claude Design handoff) |
| `docs/source/` | Source material (résumé, portfolio) used to seed content |
| `render.yaml` | Render Blueprint — every deployable service is defined here |

## Local development

```sh
cd apps/site
npm install
npm run dev      # http://localhost:4321
npm run build    # output in apps/site/dist/
```

## Deploys

Push to `main` → Render auto-deploys via the Blueprint (`render.yaml`).
Each service has a `buildFilter`, so a service only rebuilds when its own
files change. No dashboard-only config: if it isn't in `render.yaml`, it
doesn't exist.

## Adding a subdomain app

Interactive projects deploy as separate Render services on subdomains
(`<name>.kylekubik.dev`), never coupled to the main site build.

**Public app (lives in this repo):**
1. Create `apps/<name>/` with its own `package.json` (any runtime).
2. Append a service to `render.yaml` with `rootDir: apps/<name>` and a
   `buildFilter` scoped to `apps/<name>/**`.
3. Add `domains: [<name>.kylekubik.dev]` to the service and create the
   matching CNAME in Porkbun DNS.

**Private app (own repo):**
1. Create a private GitHub repo with its own `render.yaml`.
2. Connect it to Render as a new Blueprint; same DNS step as above.

Secrets never live in any repo — use Render environment variables.
```

- [x] **Step 2: Commit**

```bash
git add README.md
git commit -m "docs: add README with layout and subdomain-app playbook"
```

---

### Task 3: Scaffold Astro at apps/site

**Files:**
- Create: `apps/site/` (via `create astro`, minimal template)

**Interfaces:**
- Produces: `apps/site/package.json` with `dev`/`build`/`preview` scripts; `apps/site/src/pages/index.astro` (replaced in Task 4).

- [x] **Step 1: Scaffold the project**

Run (from repo root):
```bash
npm create astro@latest apps/site -- --template minimal --install --no-git --yes
```
Expected: `apps/site/` exists with `package.json`, `astro.config.mjs`, `src/pages/index.astro`. Dependency list contains **only** `astro`.

- [x] **Step 2: Verify the build works**

Run: `cd apps/site && npm run build`
Expected: `Complete!` with output in `apps/site/dist/index.html`. No errors.

- [x] **Step 3: Verify dependency minimalism**

Check `apps/site/package.json` — `dependencies` must contain only `astro`. If the template added anything else, remove it and re-run `npm install`.

- [x] **Step 4: Commit**

```bash
git add apps/site
git commit -m "feat: scaffold Astro site at apps/site (minimal template)"
```

---

### Task 4: On-brand placeholder page

**Files:**
- Modify: `apps/site/src/pages/index.astro` (replace template content entirely)

**Interfaces:**
- Consumes: nothing from other tasks.
- Produces: the single page Phase 1 ships. Throwaway — Phase 2 replaces it with real components; do NOT build design-system components here.

- [x] **Step 1: Replace index.astro**

Minimal, using the design tokens inline (full token system arrives in Phase 2 from Claude Design):

```astro
---
// Placeholder — replaced in Phase 2 by the real design system.
---
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Kyle Kubik — Multidisciplinary Engineer</title>
    <meta name="description" content="Portfolio of Kyle Kubik: embedded systems, hardware, and engineering leadership." />
    <style>
      body {
        margin: 0;
        min-height: 100vh;
        display: grid;
        place-items: center;
        background: #f1f1ee;
        color: #2c2f31;
        font-family: Inter, system-ui, sans-serif;
      }
      main { text-align: center; padding: 44px; }
      p.kicker {
        font-family: "IBM Plex Mono", ui-monospace, monospace;
        font-size: 12px;
        letter-spacing: 1.5px;
        text-transform: uppercase;
        color: #1f4e79;
      }
      h1 {
        font-size: 46px;
        font-weight: 600;
        letter-spacing: -1px;
        color: #1a1f24;
        margin: 8px 0;
      }
      p.note { font-size: 16px; color: #8c8b82; }
    </style>
  </head>
  <body>
    <main>
      <p class="kicker">Multidisciplinary engineer</p>
      <h1>Kyle Kubik</h1>
      <p class="note">Site in progress — full portfolio coming soon.</p>
    </main>
  </body>
</html>
```

- [x] **Step 2: Verify build and content**

Run: `cd apps/site && npm run build`
Expected: build succeeds; `dist/index.html` contains `Kyle Kubik`.

- [x] **Step 3: Commit**

```bash
git add apps/site/src/pages/index.astro
git commit -m "feat: on-brand placeholder home page"
```

---

### Task 5: Render Blueprint (render.yaml)

**Files:**
- Create: `render.yaml`

**Interfaces:**
- Produces: the static-site service definition Render reads in Task 7. Service name `kylekubik-dev-site`.

- [x] **Step 1: Write render.yaml**

```yaml
services:
  - type: web
    name: kylekubik-dev-site
    runtime: static
    rootDir: apps/site
    buildCommand: npm ci && npm run build
    staticPublishPath: dist
    domains:
      - kylekubik.dev
      - www.kylekubik.dev
    buildFilter:
      paths:
        - "**"
    envVars:
      - key: NODE_VERSION
        value: "22"
```

Note: with `rootDir` set, `buildFilter.paths` are relative to `rootDir`, so `"**"` means "only changes under `apps/site/`". Verify against current Render docs at execution time; if paths turn out to be repo-root-relative, use `apps/site/**` instead.

- [x] **Step 2: Commit**

```bash
git add render.yaml
git commit -m "feat: Render Blueprint for static site with build filter"
```

---

### Task 6: Project-scoped MCP config + push to GitHub

**Files:**
- Create: `.mcp.json`

**Interfaces:**
- Consumes: `main` branch (Task 1).
- Produces: GitHub repo `kylekubik.dev` with all commits pushed; MCP servers available next session.

- [x] **Step 1: Write .mcp.json (PRD §10 — Render + GitHub only; Postgres comes later)**

```json
{
  "mcpServers": {
    "render": {
      "type": "http",
      "url": "https://mcp.render.com/mcp"
    },
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
```

Both are OAuth-based remote servers — Claude Code will prompt to authenticate on first use; no tokens in the repo.

- [x] **Step 2: Commit**

```bash
git add .mcp.json
git commit -m "chore: project-scoped MCP config (Render, GitHub)"
```

- [x] **Step 3: Create the GitHub repo and push** ⚠️ *needs `gh` auth — if `gh auth status` fails, stop and ask Kyle to run `gh auth login`*

```bash
gh repo create kylekubik.dev --public --source . --push
```
Expected: repo visible at `github.com/<kyle's-username>/kylekubik.dev`, `main` pushed.

---

### Task 7: Render Blueprint deploy + Porkbun DNS ⚠️ *user-assisted*

**Files:** none (external services).

**Interfaces:**
- Consumes: GitHub repo (Task 6), `render.yaml` (Task 5).
- Produces: live site at `kylekubik.dev`; auto-deploy on push (Phase 1 acceptance criterion).

- [x] **Step 1: Connect the Blueprint (Kyle, in Render dashboard — one-time)**

Render Dashboard → **New → Blueprint** → select the `kylekubik.dev` GitHub repo → approve. Render reads `render.yaml` and creates `kylekubik-dev-site`.

- [ ] **Step 2: Configure DNS (Kyle, in Porkbun)**

Render's dashboard shows the exact records after Step 1; per current Render docs they are:
- `kylekubik.dev` (apex): **A** record → Render's static-site IP (shown in the service's Custom Domains tab)
- `www.kylekubik.dev`: **CNAME** → the `<service>.onrender.com` hostname

Delete Porkbun's default parked-page records. Keep any existing email-forwarding (MX) records untouched.

- [ ] **Step 3: Verify the acceptance criterion**

After DNS propagates (minutes to ~1 hour):
Run: `curl -sL https://kylekubik.dev | grep "Kyle Kubik"`
Expected: matches the placeholder `<h1>`.
Then make a trivial commit (e.g. bump README) touching `apps/site/`, push, and confirm Render auto-deploys. Also confirm a docs-only commit does **not** trigger a build (buildFilter working).

- [ ] **Step 4: Mark Phase 1 done**

Update this plan's checkboxes, commit:
```bash
git add docs/superpowers/plans/2026-07-19-phase-1-skeleton-deploy.md
git commit -m "docs: Phase 1 complete — deploy pipeline live"
git push
```
