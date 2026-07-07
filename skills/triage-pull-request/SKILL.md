---
name: triage-pull-request
description:
  Produces a risk-grouped list of features to review in a pull request.
disable-model-invocation: true
---

# Triage a pull request

## Goal

Produce a list of features to review, grouped by risk.

## Instructions

### 1. Get the issue and pull request descriptions

Ask the user for a link to the issue and a link to the pull request. Parse each
URL to determine the platform (GitHub, Atlassian) and extract the owner, repo,
and number.

For each link, try to fetch the description via an available MCP server (GitHub
or Atlassian). If no link is provided or the lookup fails, ask the user to paste
the description or write one.

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

Skim every changed file.

This step is done when every changed file has been read enough to identify its
feature area and judge its risk; a file is not done until both judgments are
made.

### 5. Write a brief summary

Write a paragraph that describes what the pull request does, without file paths
or risk levels. Draw from the issue description, the pull request description,
and the diff you have read.

This step is done when the summary is written and stands on its own without
needing the file list to make sense.

### 6. Group files by feature

Group files by feature area — what the code does. To derive the feature names,
check the issue and pull request descriptions. You can also come up with your
own concise names, for example, 'authentication', 'user management', 'database
migrations', or 'error handling'.

This step is done when every changed file appears in exactly one feature group
and every group has a name.

### 7. Assign risk levels to features

Assign each feature to one of **High**, **Medium**, or **Low**, applying the
rules in **Risk classification** below.

This step is done when every feature group has a High, Medium, or Low assignment
and a one-sentence reason for that assignment.

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

Start with the summary, then list features grouped by risk level. Use this exact
format, repeating the matching block for every feature at that risk level:

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
