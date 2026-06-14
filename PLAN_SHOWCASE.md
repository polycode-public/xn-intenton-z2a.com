# PLAN_SHOWCASE.md — the intentïon.com showcase surface

> Site-level detail doc. The cross-repo index is
> [`../agentic-lib/INTENTION_ROADMAP.md`](../agentic-lib/INTENTION_ROADMAP.md)
> (this is its §2 "site" detail). **Status: live + cleaned (2026-06-14).**

## What the showcase is now

The home page (`public/index.html`, a no-build static page) presents the intentïon
fleet and an embedded chat:

- **Repo-bar** — a fixed top-right selector (`#showcase-bar`), populated at runtime
  from [`public/agentic-lib-repositories.toml`](public/agentic-lib-repositories.toml)
  (parsed by `parseRepoToml` / `loadRepoConfig`). Each row shows the repo's
  INTENT-derived mission name, a status glyph, a transform/memory count `[N]`, and a
  "make default ●/○" toggle persisted in `localStorage` (`intention-showcase-default`).
- **Template CTA** — a "Create your own ↗" link under the repo-bar pointing at the
  `repository0` template. `repository0` is **not** a fleet instance, so it is reached
  here rather than listed as a rotating repo.
- **Per-repo panels** — the status `[N]`, the VT100 terminal, and the screenshot
  derive from each repo's `agentic-lib-logs/summary.json` (the marginalia M4 export),
  fetched raw from `raw.githubusercontent.com`.
- **Chat** — the **marginalia embed**, iframed as
  `https://marginalia.polycode.co.uk/embed.html?seed=<raw summary.json URL>&repo=<owner/slug>`.
  Tier-0 chat on marginalia's shared default graph, seeded client-side from the
  per-repo `summary.json`; the private graph is never read by the browser. CSP
  `frame-src` = `https://marginalia.polycode.co.uk`.

**Live fleet in the repo-bar (4):** `8-kyu-remember-hello-world`,
`6-kyu-understand-roman-numerals`, `3-kyu-analyze-lunar-lander`,
`2-kyu-create-markdown-compiler`. (`4-kyu-apply-cron-engine` was deleted; `sandbox`
is intentionally not showcased.)

## Done (2026-06-14 cleanup)

- Removed `repository0` + the deleted `4-kyu-apply-cron-engine` from the repo-bar
  (`agentic-lib-repositories.toml`); fixed the `index.html` hardcoded fallback +
  `localStorage` default to a live repo (`8-kyu-remember-hello-world`).
- Added the "Create your own ↗" template CTA (`#showcase-template-cta`).
- Deleted the orphaned `public/all.html` (it listed non-existent `repository0-*-stats.json`
  and was not linked from anywhere).

## Open work items

| # | Item | Status |
|---|---|---|
| S3 | **Verify the panels** render real data from `summary.json` for all 4 fleet repos (status `[N]`, VT100, screenshot) — the old engine's `agentic-lib-state.toml` / `agent-log-*.md` / `SCREENSHOT_INDEX.png` artifacts are gone; confirm nothing still 404s into a blank panel. | open |
| S2 | Wire `intention-ci` env deploys: `ci` GitHub-environment vars (`ACTIONS_ROLE_ARN`, `DEPLOY_ROLE_ARN`, `CERTIFICATE_ARN`) + the OIDC role/cert in intention-ci, so branch pushes deploy to `ci.web.xn--intenton-z2a.com`. Prod is live; ci is not yet wired. | open, low |
| — | **Embed behaviour bugs are marginalia's**, not the site's (the site only supplies the seed + iframe params): tier-0 answering about the wrong repo, no in-widget login, missing on-page context block. Tracked in `INTENTION_ROADMAP.md` §4. | marginalia |

## Deploy

`public/` is static; the deploy is `./mvnw clean verify` → `npx cdk deploy prod-web-stack`
via `.github/workflows/deploy.yml` on push to `main` (operator-gated — see the repo
`CLAUDE.md`). No build step for the HTML/JS/CSS changes themselves.

---

## History

The original showcase plan (2026-03-21, agentic-lib #1924) is superseded. What
shipped differently:

- **Chat is the marginalia embed**, not **Giscus** — the `SHOWCASE_REPOS` Giscus
  repo/category IDs and `switchGiscus()` design are obsolete; the CSP `frame-src`
  targets `marginalia.polycode.co.uk`, not `giscus.app`.
- **Each repo's mission file is `INTENT.md`**, renamed from `MISSION.md`; status is
  derived from `INTENT.md` presence + the repo's graph memory, not `MISSION_COMPLETE.md`.
- The repo set is the **kyu fleet** (config-driven via the toml), not the five
  `repository0-*` repos (string-utils / random / crucible / plot-code-lib) the
  original plan listed — those never existed under `polycode-public`.
- Per-repo data comes from `summary.json` (marginalia M4), not `agentic-lib-state.toml`.
