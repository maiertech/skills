---
name: code-review-preflight
description:
  Map a pull request before reviewing it — what it changes, where to start,
  and what could matter most. Run this before a code-review skill.
disable-model-invocation: true
---

# Code review preflight

## Goal

Orient the reviewer before a code-review skill examines the code. Produce a
**map** of the pull request: what it changes, the **review areas** it splits
into, and a priority order for reading them.

This is a map, not a code review. A code-review skill looks for findings; this
skill looks for orientation.

## Instructions

### 1. Gather pull request context

Ask for a pull request link if one is not already available. Parse the link to
identify the platform and pull request, then use the available integration to
fetch its description and target branch. Verify the response is non-empty and
readable; an empty or errored response counts as unavailable. If the platform
integration is unavailable, ask the user to paste the description and target
branch.

If the pull request links an issue, fetch the issue description too. Use
comments only when they clarify the intended change or resolve a conflict
between descriptions. If the issue or its comments are unavailable, proceed
on the pull request context and record the gap in the result.

This step is done when the pull request's description, target branch, and any
linked issue context are **verified** — non-empty, readable, and recorded —
or the gap is flagged and the user has supplied the missing pieces.

### 2. Establish the diff base

Use the target branch from the pull request when available. Otherwise ask which
branch it merges into, offering `origin/main` and `origin/develop` as defaults
and accepting another. Confirm the chosen ref resolves before using it.

This step is done when a valid base ref is recorded.

### 3. Measure the change set

Run `git diff --name-status <base>...HEAD` for the changed-file list and
`git diff --numstat <base>...HEAD` for change size. Read the full diff to ground
the review areas. If there are no changes, tell the user and stop.

Report the changed-line count as insertions plus deletions, counting only
files this skill reviews. Exclude files the reviewer can't meaningfully diff
line by line:

- **Lockfiles** — package, workspace, and similar dependency lockfiles.
- **Skill files** — anything under a `skills/` directory.
- **Agent context files** — `AGENTS.md` and `CONTEXT.md`.
- **Agent-facing documentation** — `README.md` files and content under `docs/`
  that exists for the agent or for users, not for the runtime.

Keep excluded files in the review map; only the line count is reduced. If a
file is binary, record its line count as unavailable rather than estimating
it. If multiple files are binary, note the gap in the summary so the count
isn't presented as exact.

This step is done when the changed-file list, full diff, and changed-line count
are in hand, with every excluded or uncountable change flagged.

### 4. Build the review map

Skim every changed file, including files excluded from the line count. Group
files into review areas that describe what the code does — a feature, a
dependency update, a documentation change. Each file belongs to exactly one
area, the one that best explains its change. Do not place a file in two
areas or mention it under another; if it spans areas, pick the one that best
explains the change.

For each area, capture:

- **Impact if missed** — one sentence: what user, system, or operational
  behavior breaks if a defect here escapes review.
- **Suggested focus** — one or two concrete review prompts grounded in the diff.
  Prompts orient the reviewer; they are not findings.
- **Priority** — High, Medium, or Low, by impact if a defect were missed.
  Priorities are a reading order, not a verdict on defect likelihood.

### 5. Write the preflight

Produce a summary that stands on its own without the diff, then the review map.
State the changed-line count first, then the pull request's purpose and its main
areas. Order areas by priority. Each area carries its impact, suggested focus,
and file list. Note missing intent context and excluded or uncountable changes.
Do not include findings, severity judgments, or a merge recommendation — those
belong to the code-review skill.

This step is done when the summary names the changed-line count and the purpose,
every changed file appears in exactly one area, and every area has impact,
focus, and priority.

## Priority rubric

Use the impact categories to pick the strongest signal, not the loudest one. The
code-review skill that follows may use a different severity lens; this rubric
only sets the reading order.

- **High** — security or access control, money, data integrity or migrations,
  compatibility, or a widely used critical path.
- **Medium** — meaningful user or system behavior with limited scope or blast
  radius compared to High.
- **Low** — limited blast radius, such as isolated documentation or cosmetic
  changes.

If intent context is missing or impact is uncertain, say so on the area rather
than presenting a confident classification. Do not assign priority from file
count alone; consider the behavior changed, the scope, and the stated intent.

## Output format

Use this structure:

```markdown
## Preflight summary

<2–4 sentences. Lead with the changed-line count, then the pull request's
purpose and main change areas. Mention missing intent context and any excluded
or uncountable changes.>

## Review map

### High — <change area>

**Impact if missed:** <one sentence> **Suggested focus:**
<one or two review prompts>

- `path/to/file` (added|modified|deleted)

### Medium — <change area>

...

### Low — <change area>

...
```

Omit priority headings with no areas. Include every changed file exactly once.
