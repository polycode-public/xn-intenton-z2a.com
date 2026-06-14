# Claude Code Memory - intentïon xn--intenton-z2a.com

> **DO NOT USE `cd <dir> && git ...` — USE `git -C <dir> ...` ALWAYS.**
> This is a multi-repo workspace. Each subdirectory is its own git repo.
> Using `cd` in compound commands triggers security prompts every time.
> This applies to YOU and to ANY AGENT YOU SPAWN. Never use the Agent tool
> for simple file reads or git operations in subdirectories either.

## Context Survival (CRITICAL — read this first after every compaction)

**After compaction or at session start:**
1. Read all `PLAN_*.md` files in the project root — these are the active goals
2. Run `TaskList` to see tracked tasks with status
3. Do NOT start new work without checking these first

**During work:**
- When the user gives a new requirement, add it to the relevant `PLAN_*.md` or create a new one
- Track all user goals as Tasks with status (pending → in_progress → completed)
- Update `PLAN_*.md` with progress before context gets large

**Anti-patterns to avoid:**
- Do NOT drift to side issues when a plan file defines the priority
- Do NOT silently fail and move on — throw, don't skip
- Do NOT ask obvious questions — read the plan file

## What This Repository Is

The **home page infrastructure** for intentïon at [xn--intenton-z2a.com](https://xn--intenton-z2a.com/). Single CDK stack deploying the intentïon brand website. The site is **live** at the kept apex `xn--intenton-z2a.com`.

- **Organisation**: `polycode-public` (CI = GitHub Actions; AWS auth = GitHub-OIDC)
- **Infrastructure**: Single AWS CDK stack (CloudFront + OAC, S3, Route53, CloudWatch Logs)
- **Chat**: the **marginalia chat embed** — `public/index.html` iframes `https://marginalia.polycode.co.uk/embed.html?seed=<raw summary.json URL>&repo=<owner/slug>`, with the CSP `frame-src` set to `https://marginalia.polycode.co.uk`. The embed runs tier-0 chat on marginalia's shared default graph, seeded client-side from each repo's `agentic-lib-logs/summary.json` on raw.githubusercontent.com (the private graph is never read by the browser). The showcase repo-bar (driven by `public/agentic-lib-repositories.toml`) lists the 5 fleet INTENTs.
- **Sensitive file**: `intentïon brand accounts.kdbx` — KeePass database (never commit plaintext secrets)

## Key Architecture

Single flat Maven project producing one shaded JAR (`target/web.jar`):

| File | Purpose |
|------|---------|
| `infra/main/java/.../WebApp.java` | CDK entry point — reads env vars, creates stack |
| `infra/main/java/.../WebStack.java` | Single CDK stack — S3, CloudFront OAC, Route53, CloudWatch |
| `infra/test/java/.../WebStackTest.java` | CDK assertions tests |
| `public/` | Static website content (index.html, images, error pages) |
| `.github/workflows/deploy.yml` | Single deploy workflow (ci/prod auto-selection) |
| `scripts/assume-deployment-role.sh` | Local AWS role assumption |

**Environments:** `ci` (branches → `ci.web.xn--intenton-z2a.com`) and `prod` (main → `xn--intenton-z2a.com`)

**GitHub variables:** `ACTIONS_ROLE_ARN`, `DEPLOY_ROLE_ARN`, `CERTIFICATE_ARN` — per-environment GitHub **environment variables** drive the deploy (prod set is wired; ci not yet wired). Deploy path: `./mvnw clean verify` → `npx cdk deploy {env}-web-stack` via `.github/workflows/deploy.yml` on push (main → prod).

## AWS Account Layout

- **Account**: deployed to the **intention-prod** account **813333281588** (Workloads OU, under management account 541134664601).
- **Region**: **eu-west-2** for workloads; **us-east-1** for the CloudFront ACM cert.
- **DNS**: Route53 hosted zone **`Z0816178Q6ZU5SY8ZWGZ`** in intention-prod (registrar NS repointed here; the old management-account zone is retired).
- **Certificate**: ACM cert covering apex + `*.web.xn--intenton-z2a.com` in **intention-prod / us-east-1** (its ARN is the `CERTIFICATE_ARN` env var).
- **Deploy roles** (in intention-prod): GitHub-OIDC actions role **`agentic-lib-github-actions-role`** → least-privilege deploy role **`intention-com-web-deployment-role`** (it can assume the CDK bootstrap roles plus DNS-repair perms; it is NOT AdministratorAccess).
- **SSO**: profiles `intention-ci` / `intention-prod` in `~/.aws/config` (`sso_session=polycode`, AdministratorAccess); or assume `OrganizationAccountAccessRole` from `polycode-management`.

## Test Commands

```bash
./mvnw clean verify   # Build, test, and package shaded JAR
```

## Infrastructure

**AWS CDK** — Java-based, single stack, deployed via GitHub Actions.

**Always ask before writing to AWS.** Read-only AWS operations are always permitted.

Preferred path for infrastructure: CDK code → git push → GitHub Actions deploy.

To assume the deployment role locally:
```bash
source scripts/assume-deployment-role.sh
```

## Related Repositories

| Repository | Relationship |
|------------|-------------|
| `agentic-lib` | Core SDK — provides the autonomous evolution engine |
| `repository0` | Template — the experiment this website showcases |

## Git Workflow

**You may**: create branches, commit changes, push branches, open pull requests

**You may NOT**: merge PRs, push to main, delete branches, rewrite history

**Branch naming**: `claude/<short-description>`

## Code Quality Rules

- **No unnecessary formatting** — don't reformat lines you're not changing
- **No backwards-compatible aliases** — update all callers instead
- Only run linting/formatting fixes when specifically asked

## Security Checklist

- Never commit secrets — use AWS Secrets Manager ARNs
- Never commit contents of `*.kdbx` files
- Check IAM for least privilege (avoid `Resource: "*"`)
- OIDC trust policies scoped to specific repositories
