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

### code-review-preflight

Maps a pull request before reviewing it — what it changes, where to start, and
what could matter most. Run this before a code-review skill.

```sh
npx skills add maiertech/skills --skill code-review-preflight
```

### triage-ticket

Reviews a ticket (GitHub or Jira) against the readiness checklist and surfaces
open questions the author needs to answer before it can be implemented. Stops if
the ticket cannot be fetched — no paste fallback.

```sh
npx skills add maiertech/skills --skill triage-ticket
```
