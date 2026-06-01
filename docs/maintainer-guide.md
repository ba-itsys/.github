# Maintainer Guide

This guide is intended for project maintainers. It outlines the internal processes, responsibilities, and standards for
managing repositories within this organization. While primarily for internal use, it is public to ensure transparency in
our operations.

## Repository Overview

### Centralized Documentation (`.github` repository)

The `.github` repository serves as the central hub for organization-wide standards. It contains:

- `CONTRIBUTING.md`: Guidelines for external contributors.
- `PULL_REQUEST_TEMPLATE.md`: Standardized format for PR descriptions.
- General documentation and architectural overviews.

### Shared CI/CD

Shared GitHub Actions workflows are managed within the [actions](https://github.com/ba-itsys/actions) repository. This centralization allows us to maintain
consistent CI/CD patterns across all repositories in the organization.

## Access Management

### GitHub Teams

We use GitHub Teams to manage permissions and automate reviewer assignments.

- **Permissions:** Permissions are granted to teams rather than individuals to ensure scalability.
- **Assignment:** Teams are used in `CODEOWNERS` files to automatically request reviews.

### Codeowners

Every repository **must** include a `CODEOWNERS` file (typically in the `.github/` directory).

- It ensures that the right people are automatically notified of changes in specific areas.
- It facilitates automatic PR review requests.

## Managing Contributions

### Notifications

Maintainers should ensure they are properly notified of activity in their assigned repositories.

- Configure your [GitHub notification settings](https://github.com/settings/notifications) to stay informed about new
  Issues and Pull Requests.
- Regularly check the "Pull requests" and "Issues" in the repositories you maintain.

### Handling Pull Requests

Our goal is to be helpful and professional. Follow these steps when reviewing PRs:

1. **Be Respectful:** Always maintain a polite and constructive tone.
2. **Technical Review:** Thoroughly review the code for quality, security, and adherence to project standards.
3. **Checklist Verification:** Ensure the PR author has completed the PR checklist and that it accurately reflects the
   changes.
4. **Workflow Approvals:** For first-time contributors, you must manually approve workflow runs.
    * **IMPORTANT:** Before approving, inspect the PR for malicious code, especially changes to `.github/workflows/`
      files.
6. **Commit Quality:** Verify that commit messages follow our [Commit Guidelines](#commit-guidelines), as these are used
   to generate release notes.
7. **Patience:** Contributors work asynchronously; give them time to respond to feedback. Do not feel pressured to rush
   a review.
8. **Freshness:** Ensure the PR is up-to-date with the `main` branch. Request a rebase if conflicts exist or if the
   branch is significantly behind.

### Merging

To keep our project history clean and easy to follow, we maintain a strictly linear git history. **The default "Merge
commit" option is disabled in the GitHub settings for all repositories** to avoid non-linear "merge bubbles" that can
make the history difficult to navigate.

1. **Rebase (Preferred):** Our standard approach is to use **Rebase and Merge**. We encourage contributors to use
   interactive rebasing to keep their branch up-to-date with `main` and to organize their commits into a clean, logical
   sequence before the final merge.
2. **Squash and Merge (Fallback):** If a contributor is less familiar with interactive rebasing or has a complex commit
   history, you can use **Squash and Merge** to consolidate their changes. However, **do not hesitate to ask
   contributors to clean up their commits before merging.** Even with squashing, we require high-quality, logical
   contributions. It is the contributor's responsibility to provide clear work, and the final squashed commit message
   must strictly follow our [Commit Guidelines](#commit-guidelines).

## Standards and Automation

### Commit Guidelines

We strictly follow [Conventional Commits](https://www.conventionalcommits.org/). This standardization allows us to
automate our release process and generate meaningful changelogs.

Refer to the [Release Process](release-process.md) documentation for more details.

### Releases

We use [release-please](https://github.com/googleapis/release-please) to automate versioning and release creation.

- When a PR is merged into `main`, `release-please` will update (or create) a Release PR.
- Merging the Release PR will trigger the actual release (tagging, GitHub Release creation, and deployment).

### Dependabot

Dependabot is enabled on all repositories to keep dependencies secure and up-to-date.

- **Minor/Patch:** Usually safe to merge if CI passes.
- **Major:** Exercise caution, merge only if the CI passes. 
- **Conflicts:** Use `@dependabot rebase` if the lockfile becomes out of sync.
- **Priority:** Ensure Dependabot PRs are merged before the final Release PR to include them in the next release.

## Infrastructure as Code

### Terraform Management

We use Terraform to manage repository configurations, e.g.:

- Repository creation.
- Branch protection rules.

If you need to create a new repository or change organization-level settings, please submit a PR to the terraform
repository.
