# Neutral AWS RDS Aurora Module Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create a clean-history derivative of AWS RDS Aurora module v10.2.0 that removes the nontechnical input and creation gate, preserves default AWS behavior, and opens a verified PR for reserved tag v10.2.0-neutral.1.

**Architecture:** Transform the exact upstream snapshot in temporary storage before copying it into the target worktree. The root creation local will depend only on `var.create`, the deleted input will disappear from variables, wrappers, and generated docs, the S3 import example will use the already-published neutral S3 dependency, and every other Terraform delta will be rejected by parity checks.

**Tech Stack:** Terraform 1.15.7, Terraform >= 1.11.1, AWS provider >= 6.28, HCL, terraform-docs 0.20.0, TFLint 0.59.1, actionlint 1.7.7, Git, GitHub CLI

## Global Constraints

- Worktree: `/Users/jroberts/Documents/dev/joeroberts/terraform/.worktrees/terraform-aws-rds-aurora/v10.2.0-neutral.1`.
- Branch: `neutral/v10.2.0-neutral.1`; PR base: `main`.
- Baseline: upstream `v10.2.0` at `2c3946c8191278ad974bbb077da5e03986e24f4d`.
- Reserved tag: `v10.2.0-neutral.1`; do not create or push it before merge.
- Preserve Apache 2.0, upstream authorship, contributor credit, provider metadata, and every unrelated interface and behavior.
- Every changed upstream-derived file begins with `Modified by joeroberts/terraform-aws-rds-aurora on 2026-08-12; see UPSTREAM.md.` in the appropriate comment syntax.
- The only root behavior change is replacing the nontechnical gate with `create = var.create`; upstream-default behavior remains identical.
- Upstream v10.2.0 contains no `*.tftest.hcl`; the direct-expression assertion, full HCL parity, and 13-root init/validate provide the focused behavioral proof.
- Sanitize before copying; never import upstream Git history or create a GitHub-native fork.
- Push each milestone without force. On a blocker, persist and push `docs/neutralization/BLOCKER.md`, open a coherent draft PR, update the IAM campaign journal, and continue to Security Groups.

## File Map

- Import: complete upstream v10.2.0 tree except `.git/`.
- Modify before copy: `main.tf:4`, `variables.tf:858-862`, `wrappers/main.tf:95`, and `README.md:5,366,438-442`.
- Modify after copy: `README.md`, `wrappers/README.md`, `wrappers/dsql/README.md` — derivative identity and reserved-tag sources.
- Modify after copy: `examples/s3-import/main.tf:96-97` — HTTPS Git source pinned to existing `v5.14.1-neutral.1`; remove the Registry-only version constraint.
- Modify after copy: five inherited workflow files — notices, full-SHA pins, permissions.
- Create: `UPSTREAM.md`.
- Preserve: all other upstream files and the existing `LICENSE`-only target history.

---

### Task 1: Failing Acceptance Checks and Sanitized Import

**Files:**
- Modify: `main.tf`, `variables.tf`, `wrappers/main.tf`, `README.md`
- Create: `UPSTREAM.md`
- Import: all other upstream files

**Interfaces:**
- Consumes: immutable upstream tag and target planning branch
- Produces: neutral root interface where `local.create` is controlled solely by `var.create`

- [ ] **Step 1: Verify planning branch and clone exact upstream**

```bash
test "$(git branch --show-current)" = "neutral/v10.2.0-neutral.1"
test "$(git remote get-url origin)" = "git@github.com:joeroberts/terraform-aws-rds-aurora.git"
test -z "$(git status --porcelain)"
aurora_import_root=$(mktemp -d /private/tmp/terraform-aws-rds-aurora-v10.2.0.XXXXXX)
git clone --quiet --depth 1 --branch v10.2.0 \
  https://github.com/terraform-aws-modules/terraform-aws-rds-aurora.git \
  "$aurora_import_root/source"
test "$(git -C "$aurora_import_root/source" rev-parse HEAD)" = \
  "2c3946c8191278ad974bbb077da5e03986e24f4d"
```

Expected: clean neutral branch and exact verified checkout.

- [ ] **Step 2: Prove the pristine snapshot fails interface and neutrality acceptance**

```bash
aurora_neutral_pattern="$(printf '%s|%s|%s|%s|%s|%s|%s' \
  'put''in' 'khuy''lo' 'ukr''ain' 'russ''ia' 'bela''rus' 'cri''mea' 'don''bas')"
test -n "$(rg -l -i "$aurora_neutral_pattern" "$aurora_import_root/source" \
  --hidden --glob '!.git/**')"
if rg -n '^  create = var\.create$' "$aurora_import_root/source/main.tf"; then exit 1; fi
```

Expected: upstream contains forbidden material and does not yet have the required direct creation expression.

- [ ] **Step 3: Apply the minimal technical neutralization in temporary storage**

Using `apply_patch`, make these exact changes under `$aurora_import_root/source`:

```hcl
# main.tf inside locals
create = var.create
```

- Delete the complete five-line variable block at pristine `variables.tf:858-862`.
- Delete the wrapper forwarding line at pristine `wrappers/main.tf:95`.
- Delete the banner at pristine `README.md:5` and the final five-line nontechnical section at `README.md:438-442`.
- Run terraform-docs so the deleted input row at pristine `README.md:366` disappears:

```bash
go run github.com/terraform-docs/terraform-docs@v0.20.0 markdown table \
  --lockfile=false --output-file README.md --output-mode inject \
  "$aurora_import_root/source"
```

Prepend the required notice to `main.tf`, `variables.tf`, `wrappers/main.tf`,
and `README.md`. Then assert:

```bash
rg -n '^  create = var\.create$' "$aurora_import_root/source/main.tf"
if rg -n -i "$aurora_neutral_pattern" "$aurora_import_root/source" \
  --hidden --glob '!.git/**'; then exit 1; fi
```

Expected: the direct creation expression exists exactly once and the temporary tree has zero disallowed matches.

- [ ] **Step 4: Copy sanitized tree and add provenance**

```bash
rsync -a --exclude .git "$aurora_import_root/source/" ./
test -f main.tf
test -f variables.tf
test -d modules
test -d wrappers
test -f docs/superpowers/plans/2026-08-12-rds-aurora-neutral-derivative.md
```

Create `UPSTREAM.md` recording the exact upstream URL, tag, full SHA, import
date, reserved neutral tag, Apache notice policy, derivative maintainer,
removed input/gate/wrapper/doc content, unchanged default behavior, and this
update rule: sanitize in temporary storage and never merge upstream history.
End the update procedure by requiring docs, fmt, lint, all-root validation,
parity, neutrality/history, actionlint, independent review, and a pre-tag PR.

- [ ] **Step 5: Verify the four approved neutralization deltas**

```bash
diff -u \
  <(git -C "$aurora_import_root/source" show HEAD:main.tf | perl -pe 's/create = var\.create && var\.[A-Za-z0-9_]+/create = var.create/') \
  <(sed '1d' main.tf)
diff -u \
  <(git -C "$aurora_import_root/source" show HEAD:variables.tf | sed '858,862d' | perl -0pe 's/\n+\z/\n/') \
  <(sed '1d' variables.tf | perl -0pe 's/\n+\z/\n/')
diff -u \
  <(git -C "$aurora_import_root/source" show HEAD:wrappers/main.tf | sed '95d') \
  <(sed '1d' wrappers/main.tf)
```

Expected: no output. The derivative comparison strips only the required first-line notices.

- [ ] **Step 6: Commit and push the clean import**

```bash
terraform fmt -check -recursive
git diff --check
git add . ':!docs/superpowers'
git commit -m "feat: import neutral RDS Aurora module v10.2.0"
git push -u origin neutral/v10.2.0-neutral.1
```

Expected: neutralization and import share one pushed commit, so no intermediate commit contains the removed interface/content.

---

### Task 2: Derivative Sources and Neutral S3 Example

**Files:**
- Modify: `README.md`
- Modify: `wrappers/README.md`, `wrappers/dsql/README.md`
- Modify: `examples/s3-import/main.tf`

**Interfaces:**
- Consumes: neutral Aurora module and published S3 tag `v5.14.1-neutral.1`
- Produces: reserved-tag Aurora consumer examples and a resolvable neutral S3 example dependency

- [ ] **Step 1: Run source acceptance checks before editing**

```bash
test -n "$(rg -l 'terraform-aws-modules/rds-aurora/aws|tfr:///terraform-aws-modules/rds-aurora/aws' \
  README.md wrappers -g README.md)"
test -n "$(rg -l 'terraform-aws-modules/s3-bucket/aws' examples/s3-import -g '*.tf')"
```

Expected: both assertions pass against upstream-facing sources.

- [ ] **Step 2: Add derivative identity and replace Aurora sources**

Add the standard derivative paragraph after the root description, retain
upstream authors/contributors, add `joeroberts` as derivative maintainer, use
the local `LICENSE`, and point all nine example-navigation links to
`joeroberts/terraform-aws-rds-aurora/tree/v10.2.0-neutral.1/examples/` while
preserving these exact suffixes: `autoscaling`, `dsql`, `global-cluster`,
`limitless`, `multi-az`, `mysql`, `postgresql`, `s3-import`, and `serverless`.

Replace both root README Registry examples with:

```hcl
source = "git::ssh://git@github.com/joeroberts/terraform-aws-rds-aurora.git?ref=v10.2.0-neutral.1"
```

Replace every Terraform, Terragrunt, and commented alternative in the two
wrapper READMEs with its exact `//wrappers` or `//wrappers/dsql` Git path at the
same ref. Prepend the dated HTML modification notice to both wrapper READMEs.

- [ ] **Step 3: Replace the S3 import fixture dependency**

Using `apply_patch`, replace `examples/s3-import/main.tf:96-97` with:

```hcl
source = "git::https://github.com/joeroberts/terraform-aws-s3.git?ref=v5.14.1-neutral.1"
```

Remove the Registry `version` argument and prepend the dated HCL modification
notice. Do not change any other external example dependency.

- [ ] **Step 4: Regenerate docs and prove source acceptance**

```bash
while IFS= read -r aurora_docs_dir; do
  go run github.com/terraform-docs/terraform-docs@v0.20.0 markdown table \
    --lockfile=false --output-file README.md --output-mode inject "$aurora_docs_dir"
done < <(rg -l '<!-- BEGIN_TF_DOCS -->' -g README.md | xargs -n1 dirname | sort -u)
if rg -n 'source\s*=\s*"(terraform-aws-modules/rds-aurora/aws|tfr:///terraform-aws-modules/rds-aurora/aws)' \
  README.md wrappers -g README.md; then exit 1; fi
if rg -n 'terraform-aws-modules/s3-bucket/aws' examples/s3-import -g '*.tf'; then exit 1; fi
git diff --check
```

Expected: Aurora self-sources and the S3 fixture dependency are neutral and generated docs have no deleted input row.

- [ ] **Step 5: Commit and push documentation/dependency milestone**

```bash
git add README.md wrappers/README.md wrappers/dsql/README.md examples/s3-import/main.tf
git commit -m "docs: point Aurora consumers to neutral sources"
git push
```

---

### Task 3: Workflow Pinning and Least Privilege

**Files:**
- Modify: all five files in `.github/workflows/`

**Interfaces:**
- Consumes: inherited workflow refs
- Produces: full-SHA pins, five permission maps, inert release job, valid workflow YAML

- [ ] **Step 1: Confirm unpinned refs exist, then apply exact pins**

```bash
test -n "$(rg -n -P 'uses:\s+[^\s#]+@(?![0-9a-f]{40}(?:\s|$))' .github/workflows)"
```

Apply this mapping with `apply_patch`, retaining the inherited ref in a comment:

| Ref | SHA |
| --- | --- |
| `actions/checkout@v5` | `fbc6f3992d24b796d5a048ff273f7fcc4a7b6c09 # v5` |
| `actions/setup-node@v6` | `249970729cb0ef3589644e2896645e5dc5ba9c38 # v6` |
| `actions/stale@v10` | `1e223db275d687790206a7acac4d1a11bd6fe629 # v10` |
| `amannn/action-semantic-pull-request@v6.1.1` | `48f256284bd46cdaab1048c3721360e808335d50 # v6.1.1` |
| `clowdhaus/terraform-composite-actions/directories@v1.14.0` | `462243b714d762cbcac6732098e9fdb4ab236cb7 # v1.14.0` |
| `clowdhaus/terraform-composite-actions/pre-commit@v1.14.0` | `462243b714d762cbcac6732098e9fdb4ab236cb7 # v1.14.0` |
| `clowdhaus/terraform-min-max@v2.1.0` | `a86951cbe89f4d15caec805f36aa1dd68863ae32 # v2.1.0` |
| `cycjimmy/semantic-release-action@v5` | `ba330626c4750c19d8299de843f05c7aa5574f62 # v5 branch; tag v5.0.2` |
| `dessant/lock-threads@v5` | `1bf7ec25051fe7c00bdd17e6a7cf3d7bfb7dc771 # v5` |
| `jaxxstorm/action-install-gh-release@v2.1.0` | `6096f2a2bbfee498ced520b6922ac2c06e990ed2 # v2.1.0` |

- [ ] **Step 2: Add notices and exact permissions**

Prepend the dated YAML notice to every workflow. Add top-level permissions:

- `lock.yml`: `issues: write`, `pull-requests: write`
- `pr-title.yml`: `pull-requests: read`
- `pre-commit.yml`: `contents: read`
- `release.yml`: `contents: read`
- `stale-actions.yaml`: `issues: write`, `pull-requests: write`

Retain the release job's `terraform-aws-modules` owner guard so it stays inert.

- [ ] **Step 3: Validate, commit, and push workflows**

```bash
if rg -n -P 'uses:\s+[^\s#]+@(?![0-9a-f]{40}(?:\s|$))' .github/workflows; then exit 1; fi
test "$(rg -l '^permissions:' .github/workflows | wc -l | tr -d ' ')" = "5"
go run github.com/rhysd/actionlint/cmd/actionlint@v1.7.7
git diff --check
git add .github/workflows
git commit -m "ci: pin and restrict inherited workflows"
git push
```

---

### Task 4: Complete Verification

**Files:**
- Verify: complete tracked tree/history and all 13 Terraform roots
- Do not create: tracked state, lock files, caches, or test artifacts

**Interfaces:**
- Consumes: Tasks 1-3
- Produces: evidence suitable for independent review and PR description

- [ ] **Step 1: Docs, format, lint, and 13-root validation**

```bash
while IFS= read -r aurora_docs_dir; do
  go run github.com/terraform-docs/terraform-docs@v0.20.0 markdown table \
    --lockfile=false --output-file README.md --output-mode inject "$aurora_docs_dir"
done < <(rg -l '<!-- BEGIN_TF_DOCS -->' -g README.md | xargs -n1 dirname | sort -u)
git diff --exit-code
terraform fmt -check -recursive
aurora_tflint_tmp=$(mktemp -d)
curl -fsSL https://github.com/terraform-linters/tflint/releases/download/v0.59.1/tflint_darwin_arm64.zip -o "$aurora_tflint_tmp/tflint.zip"
unzip -q "$aurora_tflint_tmp/tflint.zip" -d "$aurora_tflint_tmp"
"$aurora_tflint_tmp/tflint" --recursive \
  --only=terraform_deprecated_interpolation --only=terraform_deprecated_index \
  --only=terraform_unused_declarations --only=terraform_comment_syntax \
  --only=terraform_documented_outputs --only=terraform_documented_variables \
  --only=terraform_typed_variables --only=terraform_module_pinned_source \
  --only=terraform_naming_convention --only=terraform_required_version \
  --only=terraform_required_providers --only=terraform_standard_module_structure \
  --only=terraform_workspace_remote
aurora_plugin_cache=$(mktemp -d)
aurora_root_count=0
while IFS= read -r aurora_tf_dir; do
  aurora_root_count=$((aurora_root_count + 1))
  TF_PLUGIN_CACHE_DIR="$aurora_plugin_cache" terraform -chdir="$aurora_tf_dir" init -backend=false -input=false
  TF_PLUGIN_CACHE_DIR="$aurora_plugin_cache" terraform -chdir="$aurora_tf_dir" validate
done < <(rg --files -g versions.tf | xargs -n1 dirname | sort -u)
test "$aurora_root_count" = "13"
```

Expected: stable docs, passing fmt/lint, and 13 successful validations without AWS calls.

- [ ] **Step 2: Structural, interface, and full HCL parity assertions**

```bash
aurora_compare_root=$(mktemp -d /private/tmp/terraform-aws-rds-aurora-compare.XXXXXX)
git clone --quiet --depth 1 --branch v10.2.0 \
  https://github.com/terraform-aws-modules/terraform-aws-rds-aurora.git \
  "$aurora_compare_root/upstream"
test "$(git -C "$aurora_compare_root/upstream" rev-parse HEAD)" = \
  "2c3946c8191278ad974bbb077da5e03986e24f4d"
while IFS= read -r aurora_tf_file; do
  case "$aurora_tf_file" in
    main.tf|variables.tf|wrappers/main.tf|examples/s3-import/main.tf) continue ;;
  esac
  diff -u "$aurora_compare_root/upstream/$aurora_tf_file" "$aurora_tf_file"
done < <(git -C "$aurora_compare_root/upstream" ls-files '*.tf' | sort)
diff -u \
  <(perl -pe 's/create = var\.create && var\.[A-Za-z0-9_]+/create = var.create/' "$aurora_compare_root/upstream/main.tf") \
  <(sed '1d' main.tf)
diff -u \
  <(sed '858,862d' "$aurora_compare_root/upstream/variables.tf" | perl -0pe 's/\n+\z/\n/') \
  <(sed '1d' variables.tf | perl -0pe 's/\n+\z/\n/')
diff -u \
  <(sed '95d' "$aurora_compare_root/upstream/wrappers/main.tf") \
  <(sed '1d' wrappers/main.tf)
diff -u \
  <(perl -0pe 's#  source  = "terraform-aws-modules/s3-bucket/aws"\n  version = "~> 5\.0"#  source = "git::https://github.com/joeroberts/terraform-aws-s3.git?ref=v5.14.1-neutral.1"#' "$aurora_compare_root/upstream/examples/s3-import/main.tf") \
  <(sed '1d' examples/s3-import/main.tf)
rg -n '^  create = var\.create$' main.tf
test "$(rg -c '^  create = var\.create$' main.tf)" = "1"
```

Expected: every unchanged Terraform file is byte-identical; the four approved
files match their deterministic transforms; and the direct creation expression
occurs exactly once.

- [ ] **Step 3: Notices, actions, neutrality, history, and clean remote**

```bash
aurora_notice='Modified by joeroberts/terraform-aws-rds-aurora on 2026-08-12; see UPSTREAM.md.'
for aurora_notice_file in main.tf variables.tf wrappers/main.tf README.md \
  wrappers/README.md wrappers/dsql/README.md examples/s3-import/main.tf .github/workflows/*; do
  head -n 1 "$aurora_notice_file" | rg -Fq "$aurora_notice"
done
go run github.com/rhysd/actionlint/cmd/actionlint@v1.7.7
aurora_neutral_pattern="$(printf '%s|%s|%s|%s|%s|%s|%s' \
  'put''in' 'khuy''lo' 'ukr''ain' 'russ''ia' 'bela''rus' 'cri''mea' 'don''bas')"
if rg -n -i "$aurora_neutral_pattern" . --hidden --glob '!.git/**' --glob '!.terraform/**'; then exit 1; fi
if git grep -nEi "$aurora_neutral_pattern" $(git rev-list --all); then exit 1; fi
git diff --check
test -z "$(git status --porcelain)"
git fetch origin neutral/v10.2.0-neutral.1
test "$(git rev-parse HEAD)" = "$(git rev-parse origin/neutral/v10.2.0-neutral.1)"
test -z "$(git ls-remote --tags origin refs/tags/v10.2.0-neutral.1)"
```

Expected: all assertions pass, history is neutral, branch is synchronized, and no tag exists.

---

### Task 5: Independent Review, PR, and Campaign Status

**Files:**
- Modify only for review findings: files from Tasks 1-3
- Modify cross-repository journal: IAM worktree `docs/neutralization/CAMPAIGN-STATUS.md`
- External write: GitHub PR

**Interfaces:**
- Consumes: Task 4 evidence
- Produces: reviewed Aurora PR and durable campaign milestone

- [ ] **Step 1: Run fresh requirements and code-quality reviews**

Use `superpowers:requesting-code-review` with the design, this plan, full
`main...HEAD` diff, pristine tag, and verification evidence. Require the
reviewer to validate exact interface delta, creation behavior, notices, source
changes, action pins, and no-tag policy. Fix findings in the smallest commit,
push, rerun Task 4, and obtain a fresh re-review until clear.

- [ ] **Step 2: Create and read back the PR**

Create `/private/tmp/terraform-aws-rds-aurora-pr-body.md` containing provenance,
intentional deltas, neutral S3 fixture source, legal notices, CI hardening,
verification evidence, review result, and deferred tag. Then run:

```bash
gh pr create --repo joeroberts/terraform-aws-rds-aurora \
  --base main --head neutral/v10.2.0-neutral.1 \
  --title "feat: add neutral RDS Aurora module v10.2.0" \
  --body-file /private/tmp/terraform-aws-rds-aurora-pr-body.md
gh pr view --repo joeroberts/terraform-aws-rds-aurora \
  --json url,state,baseRefName,headRefName,commits,statusCheckRollup
```

Expected: one open PR from the neutral branch to `main`; no release tag.

- [ ] **Step 3: Update the IAM campaign journal**

In the IAM worktree, record Aurora as `PR open` with its URL, branch, final SHA,
verification summary, and `tag deferred`. Commit `docs: record Aurora campaign
milestone`, push the IAM neutral branch, read back the IAM PR if it already
exists, then move to Security Groups.
