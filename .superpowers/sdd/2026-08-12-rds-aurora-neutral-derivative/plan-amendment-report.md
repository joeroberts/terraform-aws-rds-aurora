# Aurora neutral derivative plan amendment report

- Date: 2026-08-12
- Branch: `neutral/v10.2.0-neutral.1`
- Scope: documentation-only amendment before Task 1 source import
- Authorization resolved: delete the identified forbidden `CHANGELOG.md:403` bullet and update edit, notice, parity, and transform expectations

## Changes

- Added a pre-import gate requiring this amendment to be committed, pushed without force, clean, and synchronized with the remote branch tip.
- Replaced repeat upstream cloning with `git archive` from the committed `HEAD` of the already-verified local upstream clone.
- Limited sanitized-tree changes to `README.md`, `CHANGELOG.md`, `main.tf`, `variables.tf`, and `wrappers/main.tf`, with exact first-line notices.
- Added exact README and CHANGELOG reconstruction from pristine bytes, byte comparisons against both sanitized and imported copies, and an exact five-path imported-snapshot delta assertion.
- Preserved full-HCL parity and added a deterministic final CHANGELOG parity check.
- Moved Task 1 whitespace validation after staging and made it index-aware, including untracked imported files. A root-README-only `blank-at-eof` exception is conditional on executable transform evidence; every other path retains the default check.
- Root-anchored `.git`, planning, and scratch filters handle both normal repositories and linked worktrees without hiding similarly named nested paths.
- Marked the blocker journal resolved and the amendment ledger complete.

## Verification

- All `bash` fences in the amended plan parse with `bash -n`.
- Synthetic pristine `git archive` file-set parity passed.
- Exact README/CHANGELOG byte comparisons passed, and a deliberate README mutation was rejected.
- The exact five authorized changed-path assertion passed.
- Cached whitespace checks detected a staged formerly-untracked violation.
- The conditional root README `blank-at-eof` override passed while the default non-README cached check still rejected a separate violation.
- Root `.git` directory and linked-worktree `.git` file filters passed; nested `.git` and nested planning paths were retained.
- Root-anchored staging exclusions omitted only the intended root plan path and retained the same nested path.

## State at amendment time

No upstream source was imported. No pull request, tag, or release was created. Task 1 remains unstarted pending this amendment commit being pushed and synchronized.
