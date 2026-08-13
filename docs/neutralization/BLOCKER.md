# Aurora neutralization blocker

## Scope and state

- Upstream: `terraform-aws-modules/terraform-aws-rds-aurora` v10.2.0 at `2c3946c8191278ad974bbb077da5e03986e24f4d`.
- Branch: `neutral/v10.2.0-neutral.1`.
- Amendment head: `bff5143209cbfe7ee9f12b63beab3660ea1bde8e`.
- Status: blocked after the five-round amendment breaker on 2026-08-12.

## Verification completed before the stop

- The exact five-path sanitized transform completed: `CHANGELOG.md`, `README.md`, `main.tf`, `variables.tf`, and `wrappers/main.tf`.
- Exact five-path delta parity and the imported file-set comparison completed.
- The reported 82 non-planning, non-scratch imported paths matched their pre/post SHA-256 manifests.
- HCL and neutrality work completed through the documented point of stop, including the exact transform and parity checks supported by the local SDD report.

## Load-bearing defect and unsafe work

The README whitespace evidence uses stdout-only capture with `git diff ... || :`.
That construction hides a failed producer and its stderr, converting it into an
empty successful result. It can therefore falsely authorize the narrow README
whitespace exception. The reproducible dirty source import is keyed to the
pre-amendment `import-79b7780...` scratch root and must not be reused.

No source commit or source push occurred. No pull request, reserved tag, or
release was created.

## Safe restart criteria

1. Do not reuse `import-79b7780...`.
2. Relocate or remove the reproducible dirty import before rebuilding scratch state.
3. Amend producer capture so producer status and stderr are preserved; accept only the expected whitespace exit semantics.
4. Regenerate the Task 1 brief at the current branch head.
5. Recreate a current-head scratch tree from the verified upstream clone.
6. Obtain independent review of the corrected gate before any import or pull request.
