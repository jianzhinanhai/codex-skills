---
name: static-html-public-release-flow
description: Publish or republish static HTML to a public web target. Use when deploying index.html or other static sites, choosing a provider such as GitHub Pages or a public VM, aligning browser/gh/connector auth, diagnosing deployment failures, and verifying mobile-accessible URLs.
---

# Static HTML Public Release Flow

## Purpose

Use this skill for repeatable public releases of static HTML projects. The goal is to keep the path stable across similar jobs: verify auth, create or confirm the target, publish the content, configure the chosen provider, diagnose failures, and confirm the final URL on desktop and mobile.

## Non-negotiables

- Treat browser login, `gh` auth, and GitHub connector auth as separate states.
- Do not publish until the target exists and the provider is known.
- Do not assume deployment is configured because a workflow or server config exists.
- Do not move forward without an objective success signal for the current step.
- Do not ask for passwords, tokens, SSH private keys, or 2FA codes in chat.

## Workflow

### 1. Establish the control plane

- Use the browser or Chrome for interactive login and provider settings.
- Use `gh` only when `gh auth status` is already valid.
- Use the GitHub connector for repository metadata, workflow logs, PRs, and issue operations.
- Use Computer Use only when browser UI automation is required.

### 2. Verify auth before edits

Check these in order:

1. `gh auth status`
2. Browser session can open the chosen provider pages
3. GitHub connector can see the target account, repository, or related project

If one surface is authenticated and another is not, fix the missing one before continuing.

### 3. Create or confirm the target

- Use the exact target name agreed with the user.
- Create the repository or host target if it does not exist.
- Prefer `main` unless the project already uses another branch.

### 4. Publish the site

- Add the remote or upload target content.
- Publish the full site contents.
- Confirm the published revision matches the local tip or source package.

### 5. Configure the provider

- For GitHub Pages, go to repository Settings -> Pages and set Source to `GitHub Actions`.
- For a public VM, configure the web server, proxy, TLS, and startup path.
- Save or apply the provider change.
- Re-run deployment if the first publish failed.

### 6. Verify deployment

Use objective checks:

- Workflow run shows `completed successfully`
- Public URL returns HTTP 200
- The rendered page matches the expected entry file
- Mobile-width preview opens correctly

## Failure patterns

- `gh auth status` fails: CLI is unauthenticated; fix login first.
- GitHub Pages workflow fails at `actions/configure-pages`: repository Pages source is not enabled yet.
- Repo or host creation cannot be found in the GitHub connector: create it in the browser or use another authenticated path.
- Browser login succeeds but push fails: auth is not shared across tools.
- Public URL returns 404: provider configuration is incomplete or propagation has not finished.

## Recovery

When deployment fails:

1. Check the latest workflow run and failed step.
2. Check the provider settings.
3. Confirm the target exists.
4. Confirm the published revision landed on the expected branch or host path.
5. Retry the publish only after the root cause is fixed.

## Keep the skill lean

- Put reusable steps here.
- Put longer examples or project-specific notes in separate reference files only if they are genuinely reused.
