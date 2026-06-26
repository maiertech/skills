---
name: triage
description:
  Use this skill when you are asked to triage a pull request for review.
disable-model-invocation: true
---

# Triage

## Goal

Produce a list of features to review. The list is groups features by risk (high,
medium, low).

Do not review the pull request. Do not make changes to the code. Do not make
suggestions what to review.

## Instructions

### 1. Prompt the user for the issue description

Ask the user if they can provide an issue ID.

If they provide an issue ID, check if you have access to an MCP server that you
can use to read the issue. For example the GitHub MCP server or the Atlassian
MCP server. Then read the issue description.

If they don't provide an issue ID or reading the issue for the provided ID
fails, ask the user to copy the issue description or write an issue description.

This step is done when you have added a non-empty issue description to your
context.

### 2. Prompt the user for the pull request description

Ask the user if they can provide an pull request ID.

If they provide a pull request ID, check if you have access to an MCP server
that you can use to read the pull request. For example the GitHub MCP server or
the Atlassian MCP server. Then read the pull request description.

If they don't provide a pull request ID or reading the pull request for the
provided ID fails, ask the user to copy the pull request description or write a
pull request description.

This step is done when you have added a non-empty pull request description to
your context.

### 3. Prompt the user for the base branch

Ask the user for the base branch that the pull request merges into. Offer
`origin/main` as the default option. Also offer `origin/develop` and user text
input.

This step is done when you have a non-empty base branch.

### 4. Compute the diff

Run `git diff --name-status <base>...HEAD` for a file-level overview, then
`git diff <base>...HEAD` to see the actual changes. If there are no changes,
tell the user and stop.

### 5. Read the changed files

For every changed file, read enough of the file to understand what feature area
it belongs to and how risky it is. You don't need to read every line of every
file. Read enough to categorise it.

### 6. Group files by feature

Group files by what the code does, not by file type. To derive the feature
names, check the issue and pull request descriptions. You can also come up with
your own concise names, for example, 'authentication', 'user management',
'database migrations', or 'error handling'.

### 7. Assign risk levels to features

Assign each feature to one of **High**, **Medium**, or **Low**.

Anything in one of the following categories needs to be ranked ad high risk:

- Authentication or authorization
- Payment and billing
- Data migrations
- Breaking changes
- Anything encryption

The following findings justify categorisation one level higher:

- Big refactors
- Insufficient test coverage
- Changes unrelated to the issue and pull request descriptions.
- Features with lots of files changed.
- Dependency changes not related to any feature.

### 7. Output format

Use this exact format:

```
### High 🔴
**<Feature name>**
Reason: <one sentence why this is high risk>
- `path/to/file.ext` (added|modified|deleted)
- `path/to/other.ext` (added|modified|deleted)

_Annotation:_ Repeat for every high-risk feature.

### Medium 🟠
**<Feature name>**
Reason: <one sentence why this is medium risk>
- `path/to/file.ext` (added|modified|deleted)

_Annotation:_ Repeat for every high-risk feature.

### Low 🟡

**<Feature name>**
- `path/to/file.ext` (added|modified|deleted)

_Annotation:_ Repeat for every high-risk feature.
```
