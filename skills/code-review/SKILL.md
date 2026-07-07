---
name: code-review
description:
  Review code changes for intent gaps and security risks — between what was
  specified and what was delivered.
disable-model-invocation: true
---

# Code Review

## Goal

Find gaps between intent and implementation in a set of code changes, and surface
security risks.

## Instructions

### 1. Determine what to review

Ask the user which type of review they want:

- **Branch diff** — changes between a base branch and HEAD (a pull request).
  Ask whether to diff against `origin/main` or `origin/develop`.
- **Agent diff** — uncommitted changes on the current branch (work an agent just
  did). Includes both staged and unstaged changes.
- **Feature area** — one feature area from a recent `triage-pull-request` run.
  Search the conversation history for triage output. If found, extract the
  feature areas and their file lists, present them as numbered choices, and ask
  the user to pick one. If not found, ask the user to describe the feature area
  and determine which files from the diff belong to it.

For all review types, ask the user for an issue link. Parse the URL to determine
the platform (GitHub, Jira) and extract the owner, repo, and issue number. Use
the appropriate MCP server to fetch the issue description and all comments. If
no MCP server is available, try to fetch the link directly.

Also ask for a pull request link if one exists. Parse the URL to determine the
platform (GitHub, Bitbucket) and fetch it the same way.

This step is done when you have a non-empty set of changed files and the intent
context (issue/PR descriptions, or feature area name with its reason from
triage).

### 2. Compute the diff

- **Branch diff:** Run `git diff --name-status <base>...HEAD`, then
  `git diff <base>...HEAD`.
- **Agent diff:** Run `git diff --name-status` and `git diff --cached
  --name-status`, then `git diff` and `git diff --cached`.
- **Feature area:** Filter the full branch diff to only the files in the chosen
  feature area's file list.

If there are no changes, tell the user and stop.

This step is done when you have the file-level overview and the full diff text
for the relevant files, and at least one file has changed.

### 3. Read the changed files

Read every changed file in full. Trace into unchanged code where the diff
touches interfaces, function signatures, or call boundaries. Check whether tests
exist for the changed behaviour.

For each file, assess:
- **Intent**: does every change serve the stated requirement?
- **Security**: input validation, authentication, authorization, data handling,
  error exposure.

This step is done when every changed file has been read in full and assessed for
both intent and security. A file is not done until both assessments are complete.

### 4. Report findings

For each gap between intent and implementation, classify it using the severity
levels below and record: the file and line reference, a concise description of
the gap, and a suggested fix where applicable.

This step is done when every finding is classified and every changed file has
been accounted for — either it has findings or it was assessed and found clean.

## Severity levels

1. **🚨 Critical** — Intent violated: the code does the opposite of what was asked,
   or a stated requirement is missing entirely. Security: vulnerabilities that
   expose data, allow unauthorized access, or enable injection attacks.
2. **🐛 Error** — Logic that doesn't hold up: broken edge cases, missing error
   handling, incorrect assumptions. Security: missing input validation, unsafe
   deserialization, or improper access controls.
3. **⚠️ Warning** — Scope drift: changes unrelated to the issue, or requirements
   addressed in a way that creates maintenance burden. Security: weak
   cryptography, verbose error messages, or missing rate limiting.
4. **🧭 Suggestion** — A clearer or more idiomatic approach that better serves the
   stated intent. Security: defense-in-depth improvements or safer defaults.
5. **✨ Nitpick** — Style, naming, formatting.

## Output format

Start with a `## Summary` section: overall assessment of intent alignment,
whether changes look ready to merge, and a count of findings per severity level.

Then group findings by severity. Each severity level that has findings gets its
own heading with its icon (e.g. `## 🚨 Critical`, `## 🐛 Error`). Within each
heading, list findings with file/line, description, and suggested fix.

Omit severity headings that have no findings.
