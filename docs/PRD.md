# PRD — kylekubik.dev Portfolio & Personal Ecosystem

**Audience:** Claude Code (agentic build).
**Owner:** Kyle Kubik.
**Companion docs:** `docs/design/design-system.md` (visual spec), `docs/design/portfolio-blueprint-v3.html` (reference implementation), `docs/stack-decisions.md` (full decision log), `docs/superpowers/specs/2026-07-19-repo-structure-design.md` (approved repo structure). Read all before starting.

---

## 1. Summary

Build a personal portfolio and blog at `kylekubik.dev` that doubles as a portfolio artifact in itself. The main site is content-first, statically-generated, and fast. It must be structured so individual interactive projects can later be added as isolated apps on subdomains without rearchitecting. A future consultancy pivot must be possible by activating a dormant page, not rebuilding.

**Primary audience:** technical recruiters and hiring managers (accelerated job search), with technical peers and a possible future consultancy client as secondary audiences.

**Success = a recruiter lands on the home page, understands who Kyle is and sees his 2-3 strongest projects within 8 seconds, can reach the résumé in one click, and can contact him without his email being scrapeable.**

---

## 2. Tech Stack (decided — do not re-litigate)

| Concern | Choice |
|---|---|
| Frontend framework | **Astro** (zero-JS by default; React islands only where interactivity is required) |
| Hosting | **Render** (static site for the main Astro build; web services for future subdomain apps) |
| Repo | **GitHub** |
| Domain/DNS | **Porkbun** — `kylekubik.dev` (already purchased) |
| Database (when needed) | Render managed **Postgres** |
| CI/CD + IaC | **Render Blueprints** (`render.yaml`) + git-push deploy |
| Auth (future/experimental) | **Better Auth**, self-hosted on Render Postgres — NOT in initial scope |
| Content | Astro **Content Collections** (Markdown/MDX, Zod-typed schemas) |
| Email | Porkbun/Cloudflare forwarding to existing Gmail (no hosted mailbox) |

**Constraints:**
- Minimize dependencies. The owner is explicitly anti-bloat. Do not pull in heavy UI libraries, CSS frameworks beyond what's needed, or React for anything that isn't genuinely interactive.
- No `localStorage`/browser-storage-dependent patterns in the core site.
- Everything must be reproducible via `render.yaml` — no manual dashboard-only config.

---

## 3. Design (defer to design-system.md)

Blueprint / mechanical-drawing aesthetic. Cool off-white paper `#f1f1ee`, blueprint blue `#1f4e79`, sparing orange accent `#c2622a`. Inter for prose, IBM Plex Mono for labels/data. Single ~820px column, hairlines over shadows, light mode only. Signature elements: title-block footer (plain labels), dimension-line dividers, registration ticks, faint masthead-only grid, orange right-aligned metrics. **The reference HTML is the source of truth for spacing and tone.** Do not invent a new visual direction.

Quality floor (non-negotiable): responsive to mobile, visible keyboard focus, `prefers-reduced-motion` respected, semantic HTML, Lighthouse performance ≥95 on the static pages.

---

## 4. Content Model

Define these Content Collections with Zod schemas.

**`work`** (projects AND leadership roles share one collection):
- `title` (string)
- `kind` (enum: `build` | `role`) — distinguishes hardware/software builds from leadership positions
- `tags` (string[]) — tech stack or domain, rendered in mono
- `metric` (string) — the single headline outcome, rendered in orange (e.g. "100s of vessels in production", "1st overall — FSAE Nebraska 2016")
- `status` (enum: `in-production` | `shipped` | `research` | `sunset` | `award`)
- `year` / `dateRange` (string)
- `summary` (string) — one-line for index rows
- `order` / `featured` (number/bool) — control home-page prominence
- body (MDX): Brief → Contributions → Results structure; supports embedded images and, for `build` kind, technical imagery (CAD, PCB, schematics, scope traces)

**`writing`** (blog):
- `title`, `date`, `description`, `tags` (string[]), `draft` (bool)
- body (MDX)

Seed content from the source documents in `docs/source/` (`Kyle Kubik - Portfolio.docx`, `Kyle Kubik - Resume.docx` — note the résumé is an out-of-date example, use for structure/history not current facts): ProteusCore, APEX Pro, Auburn FSAE, Nuclear Robotics (ARPA-E), Tocaro Blue Vessel Monitoring, Tocaro Blue Director role, Senior Design TA, ETRAK, iStand. Mark the strongest 3-4 as `featured`.

---

## 5. Pages / Routes

| Route | Description | Priority |
|---|---|---|
| `/` | Home: masthead + featured work rows + title-block footer | P0 |
| `/work` | Full index of all `work` entries, same row treatment | P0 |
| `/work/[slug]` | Project/experience detail; template adapts to `kind`; title-block carries project metadata | P0 |
| `/about` | Career narrative + leadership philosophy | P1 |
| `/resume` | Renders key info + one-click PDF download | P0 (recruiter-critical) |
| `/writing` | Blog index (list of `writing` entries, newest first) | P1 |
| `/writing/[slug]` | Blog post; long-form Inter body, mono for code/captions | P1 |
| `/contact` | Contact form (see §6) | P1 |
| `/services` | Dormant. Built but NOT linked in nav. For future consultancy. | P2 |
| `/404` | On-brand "off the drawing" not-found | P2 |

Persistent footer across all pages includes contact affordance (see §6).

---

## 6. Contact (anti-scraping requirement)

- **Primary:** a contact form. No email address in the page source. Form posts to a backend endpoint or a service (Formspree / Web3Forms acceptable) that forwards to Kyle's Gmail.
- Include a **honeypot** hidden field; silently discard any submission that fills it.
- **Secondary:** a JS-obfuscated `mailto:` (assembled/decoded client-side, never plain text in HTML) for people who prefer to email directly.
- No CAPTCHA (avoid friction for real visitors).

---

## 7. Architecture for Future Extensibility

**Repo structure (decided 2026-07-19 — see `docs/superpowers/specs/2026-07-19-repo-structure-design.md`):**
- One **public monorepo** (`kylekubik.dev` on GitHub): planning docs in `docs/`, design handoff artifacts in `docs/design/`, source material (résumé/portfolio) in `docs/source/`, and the Astro main site at `apps/site/`.
- Future public interactive projects (e.g. Echo Chamber, boat-track visualizer) are added as `apps/<name>/`, each deploying as a **separate Render service on a subdomain** (`project.kylekubik.dev`) via its own `render.yaml` entry with `rootDir` + `buildFilter` — never coupled to the main site build.
- **Private projects get their own private repos** (GitHub visibility is per-repo); they still deploy to subdomains via their own `render.yaml`. Secrets always live in Render environment variables, never in any repo.
- Document both paths (public app / private app) in the repo README so adding a subdomain app later is a known, repeatable step.
- `/services` page exists in the build but is unlinked — activating consultancy = adding one nav link, no rebuild.

---

## 8. Build Phases & Acceptance Criteria

**Phase 1 — Skeleton & deploy path (do this first, prove the pipeline)**
- Astro project initialized, minimal dependencies
- `render.yaml` defining the static site
- GitHub repo connected, git-push triggers a Render deploy
- `kylekubik.dev` domain pointed correctly (DNS via Porkbun)
- One placeholder page live
- ✅ *Done when:* a push to main results in an automatic deploy visible at the domain.

**Phase 2 — Design system as reusable components**
- Translate the reference HTML into Astro components: masthead, dimension-divider, work-row, title-block footer, registration ticks, base layout
- Design tokens centralized (CSS custom properties matching design-system.md exactly)
- ✅ *Done when:* the home page visually matches `portfolio-blueprint-v3.html` and components are reused, not copy-pasted.

**Phase 3 — Content collections + core pages (P0)**
- `work` collection + schema, seeded with real projects from the PDF
- `/`, `/work`, `/work/[slug]`, `/resume` built and populated
- ✅ *Done when:* all P0 pages render real content, featured projects surface on home, résumé PDF downloads.

**Phase 4 — Writing, about, contact (P1)**
- `writing` collection, `/writing`, `/writing/[slug]`, `/about`, `/contact` with working form + honeypot
- ✅ *Done when:* a blog post can be added by dropping in an MDX file; contact form delivers to Gmail; no email in page source.

**Phase 5 — Polish (P2)**
- `/services` (dormant), `/404`, SEO metadata, Open Graph tags, sitemap, favicon
- Accessibility + Lighthouse pass
- ✅ *Done when:* Lighthouse ≥95 perf / ≥95 a11y on static pages; keyboard-navigable; reduced-motion respected.

---

## 9. Out of Scope (explicitly, for now)

- Better Auth / any user accounts (future experiment, separate effort)
- Any subdomain interactive app (Echo Chamber, boat tracker) — architecture must *allow* these, but building them is not this task
- A headless/visual CMS (Astro Content Collections only for now; AstroCMS is a later option)
- Consultancy `/services` content (page shell only, dormant)
- Backend polyglot experiments (separate future services)

---

## 10. Tooling / MCP (for the build environment)

Configure at project scope (`.mcp.json`), not global:
- **Render MCP** (official, `mcp.render.com`) — deploys, logs, metrics
- **GitHub MCP** — PRs, issues, repo ops
- **Postgres MCP** — read-only role only (not needed until auth/DB phase; add later)

Keep the connected set small (3-6 max) to avoid tool bloat.

---

## 11. Open Questions for Kyle

1. Résumé: maintain as a hand-built `/resume` page that mirrors a downloadable PDF, or generate the PDF from the page? (Affects whether résumé content lives in the repo or as a static asset.)
2. Which 3-4 projects should be `featured` on the home page? (Draft assumption: ProteusCore, APEX Pro, FSAE, Nuclear Robotics.)
3. Blog: launch with any seed posts, or ship the structure empty and fill later?
4. Analytics: any interest in privacy-friendly analytics (e.g. Plausible), or keep it fully dependency-free at launch?
