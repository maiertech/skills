# maiertech/skills

A collection of AI coding skills.

## Installation

Select which skills to install:

```sh
npx skills add maiertech/skills
```

Or install individual skills by name as shown below.

The `skills` CLI installs into the AI agent you are running (Claude Code,
Cursor, Codex, Windsurf, and others — see the
[Skills.sh agent list](https://skills.sh/agent) for the full set).

## Skills

### code-review

Reviews code changes for intent gaps and security risks — between what was
specified and what was delivered. Supports three review types:

- **Branch diff** — changes between a base branch and HEAD (a pull request)
- **Agent diff** — uncommitted changes on the current branch (work an agent just
  did)
- **Feature area** — one feature area from a recent `triage-pull-request` run

Findings are classified by severity: 🚨 Critical, 🐛 Error, ⚠️ Warning, 🧭
Suggestion, ✨ Nitpick.

```sh
npx skills add maiertech/skills --skill code-review
```

### triage-issue

Creates an implementation brief from a GitHub or Jira issue — summarizes what to
build, suggests which files to change, and proposes a testing approach.

```sh
npx skills add maiertech/skills --skill triage-issue
```

### triage-pull-request

Creates a risk-based list of features and files to help a reviewer decide what
to focus on in a pull request review.

```sh
npx skills add maiertech/skills --skill triage-pull-request
```
