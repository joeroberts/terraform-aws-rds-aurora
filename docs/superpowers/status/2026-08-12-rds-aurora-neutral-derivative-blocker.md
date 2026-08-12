# Aurora neutral derivative execution blocker — blocked

- Branch: `neutral/v10.2.0-neutral.1`.
- Approved upstream: `https://github.com/terraform-aws-modules/terraform-aws-rds-aurora.git`, tag `v10.2.0`, SHA `2c3946c8191278ad974bbb077da5e03986e24f4d`.
- Amendment head: `bff5143209cbfe7ee9f12b63beab3660ea1bde8e` (`docs: make Aurora whitespace count portable`).
- Breaker: tripped after five independent amendment/review rounds on 2026-08-12. The load-bearing defect is stdout-only capture with `git diff ... || :`: it hides producer failures and stderr, and can falsely authorize the README whitespace exception.
- State at stop: the Task 1 source import remains reproducibly dirty and uncommitted. No source commit or push occurred. No pull request, tag, or release was created.
- Unsafe continuation: do not reuse the scratch import rooted at `import-79b7780...`; it predates the current amendment head and its evidence gate is not fail-closed.
- Safe restart: relocate or remove the reproducible dirty import; amend the producer capture to preserve status and stderr while accepting only the expected whitespace exit semantics; regenerate the brief at the current head; recreate a current-head scratch tree from the verified clone; and obtain independent review before any import or PR.
