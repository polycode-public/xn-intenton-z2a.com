# AGENTS.md — intentïon.com showcase site

AAIF-standard guidance for AI agents working in this repo. Project-specific
conventions and the author's working preferences live in `CLAUDE.md` (read it
first); estate-wide conventions live in `../CLAUDE.md` (the `polycode-public`
parent).

## What this is

The public showcase site for the intentïon programme, served at the apex
**`xn--intenton-z2a.com`** (domain kept across the estate move). Static
vanilla-ESM front-end in `public/`, deployed behind CloudFront/S3 by a single
**CDK-Java `WebStack`** (`infra/main/java/com/intention/web/`). CI is **GitHub
Actions** under `github.com/polycode-public` with **GitHub-OIDC→AWS**.

## Build / test / deploy

- Build + test the CDK app: `./mvnw clean verify`
- Synthesize: `npx cdk synth {env}-web-stack`
- Deploy: `npx cdk deploy {env}-web-stack --require-approval never`
- Env split: `prod` → apex `xn--intenton-z2a.com`; others → `{envName}.web.xn--intenton-z2a.com`.

## Conventions

- **CDK in Java with Maven**, JDK 25, `--release 25`. Not Gradle, not TS-CDK.
- Front-end is **raw ESM**, no framework.
- **AGPL-3.0** licensed.
- Serverless / scale-to-zero. CloudWatch for observability — no third-party vendors.
- AWS account: **intention-prod** (Workloads OU); region `eu-west-2`, `us-east-1`
  only for the CloudFront ACM cert.

## Estate-move notes (2026-06-13)

- Moved off GitLab to `github.com/polycode-public`; all `github.com/xn-intenton-z2a`
  → `github.com/polycode-public` (the punycode **domain** `xn--intenton-z2a.com`
  is unchanged — do not rewrite it).
- Hardcoded account/zone/role values are being re-pointed to the new
  `intention-prod` account; prefer parameterising via env/CI vars (see the §4.1
  list in `../polycode-projects/marginalia/PLAN_INTENTION.md`).
- The Giscus comment block is being replaced by the marginalia chat embed
  (PLAN_INTENTION §5) — coordinate before editing `public/index.html`'s chat area.
