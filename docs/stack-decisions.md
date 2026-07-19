# Personal Site / Ecosystem — Stack Decision Tracker

| # | Element | Status | Decision |
|---|---------|--------|----------|
| 1 | Frontend codebase | ✅ Decided | Astro (main site) — zero-JS by default, React islands only for interactive subdomains (Echo Chamber, boat tracker, etc.), each as separate Render services |
| 2 | Frontend hosting | ✅ Decided | Render |
| 3 | Backend codebase | 🟢 Intentionally open | Docker-based polyglot — flexible by design, no single language |
| 4 | Backend hosting | ✅ Decided | Render |
| 5 | Repo management | ✅ Decided | GitHub (portfolio visibility outweighs Bitbucket familiarity; reliability concerns noted but not disqualifying given Render handles CI/CD, not Actions) |
| 6 | Domain/DNS management | ✅ Decided | Porkbun — kylekubik.dev purchased. Email: free forwarding (Porkbun/Cloudflare) to existing Gmail, not full hosted mailbox |
| 7 | Database/data layer | ✅ Decided (default) | Render managed Postgres |
| 8 | CI/CD | ✅ Decided (default) | Render Blueprints + git-push deploy |
| 9 | IaC/provisioning | ✅ Decided (default) | Render Blueprints (render.yaml) |
| 10 | Observability | ✅ Decided (default) | Render built-in logs/metrics |
| 11 | Auth/identity | ✅ Decided | Better Auth — self-hosted, framework-agnostic, uses existing Render Postgres, no new vendor |

**Legend:** ✅ Decided · 🟡 Open / in progress · 🟢 Intentionally left flexible

---

## Tooling & Project Workflow

| Category | Tool | Notes |
|---|---|---|
| Code editor | VS Code | Daily driver |
| Content editing | Astro Content Collections | Built-in, file-based, Zod-typed MDX/Markdown — no CMS by default |
| Visual content editor (optional) | AstroCMS | Self-hosted, git-native, sits on Content Collections, built-in Claude Code agent for drafting |
| Layout/design | Claude Design | Bundled with Claude subscription, ingests codebase/design tokens, outputs live HTML, direct "handoff to Claude Code" build bundle |
| Layout/design (fallback) | Figma | Reach for if Claude Design's gaps (component systems, precise vectors) become blockers; Locofy/Builder.io Visual Copilot both output Astro-compatible code |

## Claude Code ↔ Stack Integration (MCP)

| MCP Server | Scope | Purpose |
|---|---|---|
| Render (official, `mcp.render.com`) | Project-level | Create/inspect services, logs, metrics, deploys, debugging |
| GitHub | Project-level | PR/issue management, code search, repo ops |
| Postgres | Project-level, **read-only role** | Schema inspection, queries — read-only specifically because Better Auth data lives on this DB |

**Scoping approach:** all three configured via project-level `.mcp.json` (not global `~/.claude` config) to keep them out of context during unrelated work and to contain credential exposure to this project only.

---

## Disconnected / Standalone Decisions

| Decision | Choice | Notes |
|---|---|---|
| Contact info exposure | Contact form + honeypot field (primary), JS-obfuscated mailto (secondary) | Avoids scraping without adding CAPTCHA friction; form posts via backend or a service like Formspree/Web3Forms |

---

## Content & Site Structure

**Site map:** Map C (hybrid / future-flexible) — separates "prove I can do the job" (work) from "prove I can think" (writing), with a dormant `/services` page for later consultancy activation without a rebuild.

**Page types (9):** Home · Work index · Project/experience detail (one flexible template, build vs. leadership "kind") · Writing index · Blog post · About · Résumé · Contact · 404.

**Content principle:** recruiter scans in ~8 seconds → top 2-3 projects visible without scrolling; every project uses problem→decision→impact framing with a real metric; 3-6 deep projects over a comprehensive catalog. Portfolio spine is the real professional work (ProteusCore, APEX Pro, FSAE, nuclear robotics, Tocaro Blue), not hobby projects.

## Design System

**Direction:** Blueprint / mechanical-drawing aesthetic. Adjectives: **technical, effective, interesting.** Style is a supporting layer, never the star — content leads. Blue+orange palette echoes Auburn.

**Explicitly rejected directions:** Terminal (overused), Dashboard, Telemetry. Also avoid: dark mode, SaaS gradients/glassmorphism, drop shadows, heavy animation.

**Palette:** cool off-white paper `#f1f1ee` · blueprint blue `#1f4e79` · warm orange accent `#c2622a` (used sparingly for signal only) · body `#2c2f31` · warm hairlines `#e0dccf`.

**Type:** Inter (all prose + headings) + IBM Plex Mono (labels, tags, metrics, metadata only — never reading text).

**Layout:** single ~820px reading column, hairline rules over shadowed cards, sharp corners, light mode only.

**Signature elements:** title-block footer (plain labels — Name/Role/Location/Site, no drafting metaphors) · dimension-line section dividers with ⌖ target mark · corner registration ticks · faint masthead-only grid · orange metrics right-aligned per project.

**Handoff:** `design-system.md` (full spec) + `portfolio-blueprint-v3.html` (reference implementation) → Claude Design. Recommend starting with project-detail page first to validate interpretation before propagating to all 9 page types.

---
*Last updated: content structure and blueprint design system added*
