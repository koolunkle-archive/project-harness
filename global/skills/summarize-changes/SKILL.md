---
name: summarize-changes
description: Summarizes uncommitted changes and flags anything risky. Use when the user asks what changed, wants a commit message, or asks to review their diff.
---

## Current changes

Changed and untracked files:

!`git status --short`

Size of the tracked changes:

!`git diff HEAD --stat`

Diff of the tracked changes:

!`git diff HEAD`

## Instructions

Untracked files (`??` above) do not appear in the diff; read the relevant ones before summarizing. Summarize the changes above in two or three bullet points, then list any risks you notice such as missing error handling, hardcoded values, or tests that need updating. If the diff is empty, say there are no uncommitted changes.

If the user wants a commit message, follow the `commit-history` skill for the repository's message language and format.
