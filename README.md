# maiertech/skills

A collection of AI coding skills.

[![skills.sh](https://skills.sh/b/maiertech/skills)](https://skills.sh/maiertech/skills)

## Installation

Install the whole collection:

```sh
npx skills add maiertech/skills
```

Or install a single skill by name:

```sh
npx skills add maiertech/skills --skill triage
```

The `skills` CLI installs into the AI agent you are running (Claude Code,
Cursor, Codex, Windsurf, and others — see the
[Skills.sh agent list](https://skills.sh/agent) for the full set).

## Skills

### triage

Triage a pull request before you start revieweing it. Groups changed files by
feature, then assigns each feature a risk level (High, Medium, Low) with a
one-sentence reason. Produces a structured review checklist rather than review
comments, so the reviewer knows where to spend attention.
