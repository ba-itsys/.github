# Dependabot Auto-Merge & the `automerge-check` Job

This document explains how we automate our Dependabot pull requests and how the
`automerge-check` job works. It serves as a reference for understanding the
mechanism and as a rough guide for implementing it in a new repository.

## Goal

We want to [automate Dependabot with GitHub
Actions](https://docs.github.com/en/enterprise-cloud@latest/code-security/tutorials/secure-your-dependencies/automate-dependabot-with-actions):
Dependabot PRs should be reviewed and merged automatically.

To make this safe, we allow **auto-merge (rebase)** to be enabled on a PR **if
and only if CI succeeds**. This is enforced through [repository
rulesets](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-settings-for-your-repository/about-repository-rulesets):
a status check named `automerge-check` is a **required merge check**, and
nothing (neither a human nor a bot) can merge a PR into `main` unless it passes.

## How it works

1. Dependabot opens a pull request.
2. An auto-merge workflow (e.g. `automerge-dependabot.yml`) runs on
   `pull_request_target`, fetches the PR metadata, and — for the eligible
   update types — approves the PR and enables **auto-merge (rebase)** on it.
   As a general policy, only **patch and minor** updates are auto-merged;
   **major** updates are excluded and require manual oversight.
3. GitHub will only merge the PR when all checks required by the ruleset pass:
   - `automerge-check` (aggregates all CI jobs, see below)
   - `commitlint / commitlint` (valid commit messages)
   - `DCO` (sign-off present)

   If CI fails, auto-merge simply never triggers — a human can still intervene
   manually.

## The ruleset (managed with Terraform)

Repository rulesets are managed as code in the
[github-provisioning](https://github.com/ba-itsys/github-provisioning)
repository — see `modules/repository/main.tf` for the actual configuration.
There, a ruleset on the default branch requires the status checks
`automerge-check`, `commitlint / commitlint`, and `DCO`.

> **Note:** Rulesets are only available for public repositories (a paid
> feature). The ruleset is enabled by default for every public repository and
> can be disabled per repository in `repositories.tf`.

**Important:** the ruleset does **not** list the individual CI jobs. It
requires exactly one aggregate check (`automerge-check`). This keeps the
ruleset configuration fully generic and **decouples the CI workflows from the
ruleset settings**: repositories can add, rename, or remove CI jobs without
touching the Terraform configuration, and Terraform stays DRY across all
repositories.

## The `automerge-check` job

To provide the required check, every repository defines a small, generic
`automerge-check` job in its CI workflow. The job declares a `needs` list of
all jobs that must succeed before a merge is allowed, and fails if any of them
did not succeed:

```yaml
  # The name `automerge-check` is configured in the repository ruleset as a
  # required merge check.
  # See: https://github.com/ba-itsys/.github/blob/main/docs/dependabot/automerge-check.md
  automerge-check:
    runs-on: ubuntu-latest
    needs:
      - build
      - test
    if: always()
    steps:
      - name: Verify all jobs succeeded
        run: |
          results="${{ needs.build.result }} ${{ needs.test.result }}"
          echo "Job results: $results"
          for result in $results; do
            if [ "$result" != "success" ]; then
              echo "At least one job did not succeed"
              exit 1
            fi
          done
          echo "All jobs succeeded"
```

### Requirements

Keep the following in mind when configuring the job:

- **`needs` is your merge gate.** Every job whose success should gate merging
  (one or multiple) **must** be listed in the `needs` block. If a job is not
  listed here, its result does not block the merge.
- **Every job in `needs` must also be referenced in the `results` variable.**
  Otherwise its result is not checked and it does not gate the merge. When you
  add a new CI job, update *both* places.
- **All listed jobs must always run and succeed.** Only reference jobs that
  are expected to run unconditionally. If a needed job is *skipped* or
  *canceled*, `automerge-check` fails as well (the loop only accepts the
  result `success`).
- **`if: always()` is required.** Without it, the job would be skipped when a
  needed job fails — and a skipped job does not report a failing status check.
  With `always()`, the job always runs and reports an explicit failure.

### Behavior

| CI result                          | `automerge-check` | Resulting behavior                |
| ---------------------------------- | ----------------- | --------------------------------- |
| All needed jobs succeed            | succeeds          | Auto-merge (rebase) merges the PR |
| Any needed job fails or is skipped | fails             | PR stays open, no auto-merge      |

## Adding the check to a new repository

The ruleset is created automatically by Terraform when the repository is added
to `repositories.tf` in
[github-provisioning](https://github.com/ba-itsys/github-provisioning). On the
repository side, you only have to:

1. Add an `automerge-check` job to the repository's CI workflow (see
   [above](#the-automerge-check-job)).
2. List in `needs` all jobs that must pass before a merge (e.g. build, test,
   lint, ...).
3. Verify in the repository settings that the required check `automerge-check`
   is reported by a running workflow.

If the repository should *not* participate in auto-merge, disable the ruleset
for it in `repositories.tf` in the
[github-provisioning](https://github.com/ba-itsys/github-provisioning)
repository instead.

## Related

- [Automate Dependabot with Actions](https://docs.github.com/en/enterprise-cloud@latest/code-security/tutorials/secure-your-dependencies/automate-dependabot-with-actions)
- [Securely using pull_request_target](https://docs.github.com/en/actions/reference/security/securely-using-pull_request_target) —
  the auto-merge workflow runs in the context of the target branch with secrets
  available; never check out and execute code from the PR.
- [Maintainer Guide](../maintainer-guide.md)
- [Release Process](../release-process.md)

