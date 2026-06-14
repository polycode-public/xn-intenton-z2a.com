# intentïon

**intentïon: The feedback loop of innovation**

The public website for intentïon at [xn--intenton-z2a.com](https://xn--intenton-z2a.com/).

## What This Is

A minimalist single-page website presenting the intentïon brand. Dark text on an animated fog background, with a live chat embed powered by [marginalia](https://marginalia.polycode.co.uk/).

The site showcases autonomous code evolution experiments running on the fleet of INTENT repositories, powered by [agentic-lib](https://github.com/polycode-public/agentic-lib). The repo-bar (driven by `public/agentic-lib-repositories.toml`) lets visitors switch between the fleet INTENTs; for each one the page fetches its `INTENT.md` and `agentic-lib-logs/summary.json` from GitHub.

The site is a **read-only view** of the fleet — it renders each repo's exported `summary.json` and drives nothing itself. The repos behind it cannot self-drive either; delivery is driven by one of **three hands**: a human (raising issues, dispatching workflows, reviewing and merging PRs by hand), Claude + the benchmark harness (the `intention` Claude Code session orchestrating a run), or marginalia (the supervisor graph, one piece at a time via its `repo_dispatch` actuator).

## Pronunciation

intentïon. /ɪnˈtɛnʃən/. The diaeresis is a style thing. Pronounce your intentïon as you please.

## Chat

Chat is the **marginalia embed**. `public/index.html` iframes
`https://marginalia.polycode.co.uk/embed.html?seed=<raw summary.json URL>&repo=<owner/slug>`,
and the Content-Security-Policy restricts `frame-src` to `https://marginalia.polycode.co.uk`.

The embed runs tier-0 chat on marginalia's shared default graph, seeded
client-side from each repo's `agentic-lib-logs/summary.json` on
`raw.githubusercontent.com`. The private graph is never read by the browser.

## Infrastructure

A single AWS CDK (Java) stack — `WebStack` — deploying:

- **S3** origin bucket for static content
- **CloudFront** + Origin Access Control (OAC) for distribution
- **Route53** for DNS
- **CloudWatch Logs** for access logging
- **ACM** certificate (in us-east-1) for the CloudFront distribution

**Region:** `eu-west-2` for workloads; `us-east-1` for the CloudFront ACM certificate.

**Account:** deployed to the **intention-prod** account `813333281588` (Workloads OU,
under management account `541134664601`). DNS is the Route53 hosted zone
`Z0816178Q6ZU5SY8ZWGZ` in intention-prod.

**Environments:** `ci` (branches → `ci.web.xn--intenton-z2a.com`) and `prod`
(main → apex `xn--intenton-z2a.com`).

## Deployment

CI is **GitHub Actions** under the `polycode-public` organisation; AWS auth is via
**GitHub OIDC**. On push, `.github/workflows/deploy.yml` runs:

```bash
./mvnw clean verify
npx cdk deploy {env}-web-stack
```

The OIDC actions role `agentic-lib-github-actions-role` assumes the
least-privilege `intention-com-web-deployment-role` in intention-prod. The
per-environment GitHub variables `ACTIONS_ROLE_ARN`, `DEPLOY_ROLE_ARN`, and
`CERTIFICATE_ARN` drive the deploy.

## Development

```bash
npm install
./mvnw clean verify
```

See `_developers/` for scripts, brand assets, and setup docs.

## Social

- [LinkedIn](https://www.linkedin.com/company/intentïon)
- [Facebook](https://www.facebook.com/profile.php?id=61559328506140)
- [Twitter/X](https://twitter.com/intentiionai)
- [Instagram](https://www.instagram.com/intentiionaii)
- [LinkTree](https://linktr.ee/intentiion)

## License

AGPL-3.0 — see [LICENSE](LICENSE).

Copyright (c) 2024-2026 Polycode Limited.
</content>
</invoke>
