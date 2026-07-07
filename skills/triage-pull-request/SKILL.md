---
name: triage-pull-request
description:
  "Use this skill when you are asked to triage a pull request for review."
disable-model-invocation: true
---

# Triage Pull Request

## Goal

Triage the pull request. Produce a list of features to review, grouped by risk.
Do not make suggestions for what to review — only produce the list.

## Instructions

### 1. Get the issue and pull request descriptions

Ask the user for both an issue ID and a pull request ID.

For each ID the user provides, check whether you have access to an MCP server
that can read it — the GitHub MCP server or the Atlassian MCP server are the
common ones — and read the description through it. If the user does not provide
an ID, or the MCP lookup fails, ask the user to paste the description or write
one.

This step is done when both a non-empty issue description and a non-empty pull
request description are in your context.

### 2. Prompt the user for the base branch

Ask the user for the base branch that the pull request merges into. Offer
`origin/main` as the default, `origin/develop` as a second option, and a
free-text option to type in any other branch.

This step is done when a non-empty base branch is recorded in your context.

### 3. Compute the diff

Run `git diff --name-status <base>...HEAD` for a file-level overview, then
`git diff <base>...HEAD` to see the actual changes. If there are no changes,
tell the user and stop.

This step is done when you have both the file-level overview and the full diff
in your context, and at least one file has changed.

### 4. Read the changed files

Skim every changed file. You don't need to read every line.

This step is done when every changed file has been read enough to identify its
feature area and judge its risk; a file is not done until both judgments are
made.

### 5. Group files by feature

Group files by what the code does, not by file type. To derive the feature
names, check the issue and pull request descriptions. You can also come up with
your own concise names, for example, 'authentication', 'user management',
'database migrations', or 'error handling'.

This step is done when every changed file appears in exactly one feature group
and every group has a name.

### 6. Assign risk levels to features

Assign each feature to one of **High**, **Medium**, or **Low**.

This step is done when every feature group has a High, Medium, or Low assignment
and a one-sentence reason for that assignment.

### 7. Write a brief summary

Write a short, human-readable paragraph (2–4 sentences) that describes what the
pull request does in plain language. Draw from the issue description, the pull
request description, and the diff you have read. Do not list files or repeat
risk levels — just explain the change as you would to a colleague.

This step is done when the summary is written and stands on its own without
needing the file list to make sense.

## Risk classification

Anything in the following categories is always high risk:

- Authentication or authorization
- Payment and billing
- Data migrations
- Breaking changes
- Anything encryption

The following findings justify categorisation one level higher, capped at High:

- Big refactors
- Insufficient test coverage
- Changes unrelated to the issue and pull request descriptions.
- Features with lots of files changed.
- Dependency changes not related to any feature.

If none of the above apply, the feature is Low.

## Output format

Start with the summary, then list features grouped by risk level. Use this
exact format, repeating the matching block for every feature at that risk level:

```
## Summary

<2–4 sentence plain-language description of what the pull request does>

### High ⚡⚡⚡
**<Feature name>**
Reason: <one sentence why this is high-risk>
- `path/to/file.ext` (added|modified|deleted)
- `path/to/other.ext` (added|modified|deleted)

### Medium ⚡⚡
**<Feature name>**
Reason: <one sentence why this is medium-risk>
- `path/to/file.ext` (added|modified|deleted)

### Low ⚡
**<Feature name>**
Reason: <one sentence why this is low-risk>
- `path/to/file.ext` (added|modified|deleted)
```
