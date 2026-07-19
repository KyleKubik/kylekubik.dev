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
