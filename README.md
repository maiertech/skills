# maiertech/skills

A collection of AI coding skills.

## Installation

Install the whole collection:

```sh
npx skills add maiertech/skills
```

Or install a single skill by name:

```sh
npx skills add maiertech/skills --skill triage-pull-request
```

The `skills` CLI installs into the AI agent you are running (Claude Code,
Cursor, Codex, Windsurf, and others — see the
[Skills.sh agent list](https://skills.sh/agent) for the full set).

## Skills

### triage-issue

Creates an implementation brief from a GitHub or Bitbucket issue — summarizes what to build, suggests which files to change, and proposes a testing approach.

### triage-pull-request

Creates a risk-based list of features and files to help a reviewer decide what
to focus on in a pull request review.
