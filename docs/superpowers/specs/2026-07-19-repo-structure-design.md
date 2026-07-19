# Repo Structure Design — kylekubik.dev

**Date:** 2026-07-19
**Status:** Approved by Kyle
**Companion docs:** `docs/PRD.md`, `docs/stack-decisions.md`, `docs/design/design-system.md`

## Decision

One **public monorepo** (`kylekubik.dev` on GitHub) encapsulates the whole ecosystem: planning docs, design handoff artifacts, and the Astro main site as the first app under `apps/`. Future subdomain apps that double as portfolio artifacts are added as `apps/<name>` with their own Render service. **Private projects get their own private repos** — GitHub visibility is per-repo, so private code never enters the monorepo.

## Structure

```
kylekubik.dev/
├── README.md                         # overview + "add a subdomain app" playbook (PRD §7)
├── render.yaml                       # Render Blueprint — static site now, future services appended
├── .mcp.json                         # Render + GitHub MCP, project-scoped (PRD §10)
├── .gitignore
├── docs/
│   ├── PRD.md
│   ├── stack-decisions.md
│   ├── superpowers/specs/            # design docs like this one
│   └── design/
│       ├── design-system.md          # Claude Design's spec, versioned with the code
│       └── portfolio-blueprint-v3.html   # reference implementation (source of truth)
└── apps/
    └── site/                         # main Astro site — one deployable unit
        ├── package.json
        ├── astro.config.mjs
        ├── tsconfig.json
        ├── public/
        │   ├── fonts/                # Inter + IBM Plex Mono, self-hosted
        │   ├── images/               # CAD/PCB/scope imagery for project pages
        │   ├── resume.pdf
        │   └── favicon.svg
        └── src/
            ├── styles/
            │   ├── tokens.css        # CSS custom properties, verbatim from design-system.md
            │   └── global.css
            ├── layouts/
            │   └── BaseLayout.astro
            ├── components/           # Masthead, DimensionDivider, WorkRow, TitleBlock, ContactForm
            ├── content/
            │   ├── work/             # *.mdx project + role entries
            │   └── writing/          # *.mdx blog posts
            ├── content.config.ts     # Zod schemas for work + writing collections
            └── pages/                # index, work/, writing/, about, resume, contact, services, 404
```

## Rationale

- **Monorepo-ready at near-zero cost.** Render Blueprints support per-service `rootDir` and `buildFilter`, so `apps/echo-chamber/` can later deploy as its own container without coupling to or triggering the main site build — satisfying PRD §7's decoupling requirement inside one repo.
- **Public by default.** GitHub-for-portfolio-visibility (stack decision #5) means recruiters can browse the source. Secrets live in Render environment variables, never in the repo, so "private" only matters for code Kyle doesn't want readable — and that code was never portfolio content anyway.
- **`docs/design/` is the Claude Design handoff boundary.** Claude Design owns the spec and reference HTML there; its output is translated into `apps/site/src/components/` and `src/pages/`. Design tokens live in exactly one place (`tokens.css`).
- **Considered and rejected:** fully private monorepo (defeats visibility rationale); Astro at repo root with separate repos per app (scatters the ecosystem, awkward home for docs); git submodules for private code (daily-work and deploy friction).

## Subdomain-app pattern (to be documented in README)

- **Public app:** add `apps/<name>/`, append a service entry to `render.yaml` with `rootDir: apps/<name>` and a `buildFilter` scoped to that path, point `<name>.kylekubik.dev` DNS at the new service.
- **Private app:** new private GitHub repo with its own `render.yaml`; same DNS step. Nothing in the monorepo changes.

## Next steps

Implementation plan (via writing-plans) covering PRD Phase 1: scaffold this structure, move existing docs into place, init Astro under `apps/site/`, `render.yaml`, GitHub repo, Render service, DNS.
