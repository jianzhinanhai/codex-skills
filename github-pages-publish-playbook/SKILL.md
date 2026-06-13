---
name: github-pages-publish-playbook
description: Publish or republish static HTML projects to GitHub Pages. Use when creating or validating a GitHub repo, aligning browser/gh/connector auth, enabling Pages, diagnosing workflow failures, or verifying the final deployment URL for similar projects.
---

# GitHub Pages Publish Playbook

## Purpose

Use this skill for repeatable GitHub Pages publishes of static HTML projects. The goal is to keep the path stable across similar jobs: verify auth, create or confirm the repository, push the content, enable Pages, diagnose the workflow, and confirm the final URL.

## Non-negotiables

- Treat browser login, `gh` auth, and GitHub connector auth as separate states.
- Do not push until repository existence is confirmed.
- Do not assume Pages is enabled because a workflow file exists.
- Do not move forward without an objective success signal for the current step.
- Do not ask for passwords, tokens, SSH private keys, or 2FA codes in chat.

## Workflow

### 1. Establish the control plane

- Use the browser or Chrome for interactive GitHub login and repository settings.
- Use `gh` only when `gh auth status` is already valid.
- Use the GitHub connector for repository metadata, workflow logs, PRs, and issue operations.
- Use Computer Use only when browser UI automation is required.

### 2. Verify auth before edits

Check these in order:

1. `gh auth status`
2. Browser session can open GitHub pages
3. GitHub connector can see the target account or repository

If one surface is authenticated and another is not, fix the missing one before continuing.

### 3. Create or confirm the repository

- Use the exact target repo name agreed with the user.
- Create the repo if it does not exist.
- Prefer pushing to `main` unless the project already uses another branch.

### 4. Push the site

- Add the remote.
- Push the full site contents.
- Confirm the pushed commit hash matches the local tip.

### 5. Enable Pages

- For GitHub Actions, go to repository Settings -> Pages.
- Set Source to `GitHub Actions`.
- Save.
- Re-run the workflow if the first publish failed.

### 6. Verify deployment

Use objective checks:

- Workflow run shows `completed successfully`
- Pages URL returns HTTP 200
- The rendered page matches the expected entry file

## Failure patterns

- `gh auth status` fails: CLI is unauthenticated; fix login first.
- Pages workflow fails at `actions/configure-pages`: repository Pages source is not enabled yet.
- Repo creation cannot be found in the GitHub connector: create it in the browser or use another authenticated path.
- Browser login succeeds but push fails: auth is not shared across tools.

## Recovery

When deployment fails:

1. Check the latest workflow run and failed step.
2. Check repository Settings -> Pages.
3. Confirm the remote repository exists.
4. Confirm the push landed on the expected branch.
5. Retry the publish only after the root cause is fixed.

## Keep the skill lean

- Put reusable steps here.
- Put longer examples or project-specific notes in separate reference files only if they are genuinely reused.
