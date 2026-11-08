---
name: commit-history
description: Writes commit messages that match each repository's existing convention, and safely rewrites the messages of specific past commits (including already-pushed ones) while keeping order, content, merges, authors and dates. Use when the user asks for a commit, a commit message, to translate or fix old commit messages, or to change history before or after a push.
---

# Commit messages and history rewrites

## 1. Writing a new commit message

1. **Read the convention first**: `git log --format=%s -30`. Match the language (Korean or English) and the prefix style the repository already uses (e.g. `feat:`, `docs:`, `refactor:`, or `add:`). Do not introduce scopes like `refactor(structure):` unless the history already uses them.
2. **Describe the actual change**, based on `git diff --cached --stat` and the diff itself — not on the request wording. Rename-only, content-only and docs-only changes get different prefixes.
3. **Body only when it adds information**: list concrete changes as `- ...` lines for multi-part commits.
4. **No attribution trailers**: never add `Co-Authored-By`, session links or tool signatures. The user turns these off with the `attribution` setting; do not add them by hand either.
5. **Split commits when it preserves history**: if files are renamed *and* heavily edited, commit the pure renames first and the content changes second, so `git log --follow` keeps working.

## 2. Rewriting messages of specific past commits

Use this when the user lists commits (or a range) whose messages should change while everything else stays the same.

1. **Inspect**: `git log --graph --format='%h %H %p | %s'`. Note merge commits — `git rebase -i` can flatten or break them, so prefer the filter approach below.
2. **Confirm the new messages** with the user in a table (hash, old, new) before rewriting. Read each commit's `git show --stat` so the new message matches what the commit really did.
3. **Back up**: `git branch backup-main`.
4. **Rewrite by full hash** — one file per commit containing the new message:

   ```bash
   MSGDIR=/path/to/msgs FILTER_BRANCH_SQUELCH_WARNING=1 git filter-branch -f --msg-filter \
     'if [ -f "$MSGDIR/$GIT_COMMIT" ]; then cat "$MSGDIR/$GIT_COMMIT"; else cat; fi' \
     -- main ^<oldest-target>^
   ```

   On Windows, write the message files with UTF-8 explicitly (e.g. Python with `encoding="utf-8"`); shell pipelines can mangle Korean text.
5. **Verify before pushing**:
   - final tree identical: `git rev-parse main^{tree}` equals `backup-main^{tree}`
   - same commit and merge counts, same authors and dates (`git log --format='%T %an %ad'` diff)
   - only the intended subjects/bodies changed (`git log --format=%B` diff)
6. **Push** with `git push --force-with-lease origin main`, only after the user confirms. If the push is blocked, give the user the exact command to run.
7. **Clean up on request**: `git update-ref -d refs/original/refs/heads/main`, `git branch -D backup-main`, then optionally `git reflog expire --expire=now --all && git gc --prune=now`. Warn that this makes the old history unrecoverable locally.

Hashes of every rewritten commit and its descendants change. Subtree merge messages that quote old hashes (`Add 'x/' from commit '...'`) will point to hashes that no longer exist; mention this when it applies.
