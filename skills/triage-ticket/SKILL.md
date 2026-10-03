---
name: triage-ticket
description:
  Reviews a ticket against the readiness checklist and surfaces open questions
  the author needs to answer before it can be implemented.
disable-model-invocation: true
---

# Triage a ticket

## Goal

Give a `_ready_` / `not ready` verdict on the ticket. If it is `_ready_`, say
so. If not, surface every gap as a categorized open question so the author can
refine the ticket.

The skill does not plan the implementation, list files, or suggest tests.
Refining the ticket is a prerequisite for those steps; until the ticket is
`_ready_`, planning is premature.

## Readiness checklist

Walk the ticket against these seven areas:

- **Problem & Context** — who needs this and why.
- **Scope** — what is in, what is explicitly out.
- **Acceptance Criteria** — Given/When/Then or concrete test cases.
- **Edge Cases & Errors** — validation errors, timeouts, fallbacks.
- **Technical Design** — DB changes, API payloads, code boundaries.
- **Dependencies** — external blockers, access keys, mocks.
- **Testing Plan** — automated test levels and manual checks.

## Instructions

### 1. Fetch the ticket

Ask the user for the ticket URL. Parse it to determine the platform (GitHub or
Jira) and extract the owner, repo / project key, and ticket number. Fetch the
ticket description and the comments that change the spec — ignore status pings,
bot comments, and merge noise.

If the URL cannot be parsed or the fetch fails, stop. Surface the reason the
fetch failed and ask the user to fix the access. Do not fall back to a pasted
copy — the fetch is the inspection gate; bypassing it defeats the gate.

This step is done when the ticket description and the spec-relevant comments are
in your context.

### 2. Walk the checklist

For each of the seven areas, judge whether the ticket establishes what the area
requires. Quote the ticket where it is ambiguous. When uncertain, mark the area
as a gap — the human will confirm.

This step is done when every one of the seven areas has been judged `_ready_` or
marked as a gap, every gap has at least one open question, and the State
paragraph is written.

### 3. Output the triage note

Default shape — the ticket is ready:

```markdown
## State

<One paragraph: what the ticket establishes.>

## Ready

The ticket establishes every checklist area. No refinement needed.
```

Otherwise — gaps exist. Replace `## Ready` with `## Open questions` and group by
checklist area; omit areas that are `_ready_`:

```markdown
## State

<One paragraph: what the ticket establishes, what it leaves open.>

## Open questions

### Problem & Context

- <question>

### Scope

- <question>

### Edge Cases & Errors

- <question>

### Technical Design

- <question>

### Dependencies

- <question>

### Testing Plan

- <question>
```
