# Aurora neutral derivative execution blocker

- Branch: `neutral/v10.2.0-neutral.1`.
- Approved upstream: `https://github.com/terraform-aws-modules/terraform-aws-rds-aurora.git`, tag `v10.2.0`, SHA `2c3946c8191278ad974bbb077da5e03986e24f4d`.
- The approved Task 1 edit set enumerates `main.tf`, `variables.tf`, `wrappers/main.tf`, and `README.md`, while strict parity and repository-wide neutrality checks require the entire imported snapshot to be clean. The upstream snapshot has one additional neutrality match at `CHANGELOG.md:403`, outside that edit set. This creates a direct conflict between the enumerated edits/strict parity and repository-wide neutrality.
- No source change, PR, tag, or release was created.
- Safe resolution requires explicit authorization to neutralize the changelog entry and update the parity/notice expectations accordingly. Per the user's instruction, execution moved to Security Groups.
