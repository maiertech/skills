---
name: triage-issue
description: Produces a brief implementation plan from an issue so you can start coding immediately.
disable-model-invocation: true
---

# Triage an issue

## Goal

Produce a brief — what to build, where to build it, and how to verify it — so
you can start coding immediately.

## Instructions

### 1. Fetch the issue

Ask the user for the issue URL. Parse it to determine the platform (GitHub,
Bitbucket) and extract the owner, repo, and issue number. Use the appropriate
MCP server to fetch the issue description and all comments.

If the URL cannot be parsed or the MCP lookup fails, ask the user to paste the
full issue text.

This step is done when the issue description and all comments are in your
context.

### 2. Summarize the issue

Write a 2–3 sentence plain-language summary of what needs to be done,
incorporating any clarifications or scope changes from the comments. State the
desired end state, not the process to get there.

This step is done when the summary stands on its own without needing the
original issue text.

### 3. Explore the codebase

Explore the repository to understand its architecture. Search for existing code
in the issue's domain — the feature area, affected components, or relevant data
models. Trace the call path. Read the files you expect to change, enough to
understand their current behaviour and interfaces.

This step is done when you can name every file you expect to modify and explain
why each one needs to change.

### 4. List files to change

List the specific files that need to be created, modified, or deleted, with a
one-sentence reason for each. Group by feature area if there are more than five
files.

This step is done when every anticipated change is listed with its reason.

### 5. Suggest testing approach

Suggest how to verify the implementation. Match the existing test infrastructure
where one exists. Cover both automated tests and manual verification (UI, API,
CLI). Call out edge cases from the issue or comments.

This step is done when the testing suggestions cover both automated and manual
verification paths.

## Scope assessment

An issue is too large when either condition holds:

- You cannot describe the full implementation in one sentence.
- It touches more than three unrelated areas of the codebase.

When an issue is too large, propose a split into smaller, independently
shippable sub-issues with clear boundaries.

## Output format

Use this exact format:

```
## Summary

<2–3 sentence plain-language description of what needs to be done>

## Scope

<One of: "Fits one implementation." or "Too large — consider splitting:" followed by proposed sub-issues>

## Files to change

- `path/to/file` — <one-sentence reason>
- `path/to/other` — <one-sentence reason>

## Start here

<One sentence: which file to open first and what to do in it>

## Testing

- <testing suggestion>
- <testing suggestion>
```
