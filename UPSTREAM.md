# Upstream Provenance

This repository is an independently maintained derivative of the Terraform AWS RDS Aurora module.

- Upstream repository: https://github.com/terraform-aws-modules/terraform-aws-rds-aurora.git
- Upstream tag: `v10.2.0`
- Upstream commit: `2c3946c8191278ad974bbb077da5e03986e24f4d`
- Import date: `2026-08-12`
- Neutral release: `v10.2.0-neutral.1`
- Derivative maintainer: `joeroberts/terraform-aws-rds-aurora`

## License and notices

The upstream Apache License 2.0, copyright, authorship, contributor credit, and provider metadata are preserved. Every upstream-derived file changed by this derivative begins with `Modified by joeroberts/terraform-aws-rds-aurora on 2026-08-12; see UPSTREAM.md.` in the file's appropriate comment syntax. The repository `LICENSE` remains the authoritative license text.

## Neutralization

The import removes one nontechnical boolean input, the corresponding root creation gate, its wrapper forwarding expression, and the related README and changelog content. Root creation is now controlled solely by `var.create`.

The removed input defaulted to `true`, and the wrapper used the same fallback. Therefore, this neutralization leaves default resource-creation behavior unchanged while removing the unrelated interface and content. No other upstream interface or behavior is intentionally changed by this import.

## Updating from upstream

1. Select an immutable upstream tag and verify its full commit SHA in a single retained local clone.
2. Archive that verified commit without Git metadata.
3. Sanitize the archived snapshot in temporary storage before copying it into this repository.
4. Never merge upstream history into this repository; import only the sanitized file snapshot so the derivative retains independent history.
5. Preserve the Apache License 2.0, upstream authorship and contributor credit, and add derivative modification notices to every changed upstream-derived file.
6. Before reserving or publishing a new neutral tag, complete documentation checks, Terraform formatting, linting, validation of every Terraform root, upstream-parity checks, neutrality and history checks, actionlint, independent review, and a pre-tag pull request.
