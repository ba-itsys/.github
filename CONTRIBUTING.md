# Contributing to ba-itsys Projects

Thanks for your interest in contributing! This document provides general guidelines for contributing to our repositories. Since most of our projects are Keycloak extensions built with Maven, these steps apply to the majority of our codebase.

## Table of Contents

- [Getting Started](#getting-started)
- [Development Standards](#development-standards)
- [Checks and Validation](#checks-and-validation)
- [Commit Guidelines](#commit-guidelines)
- [Pull Request Process](#pull-request-process)
- [License](#license)

---

## Getting Started

1.  **Fork the Repository**: Start by forking the repository to your own GitHub account.
2.  **Create a Branch**: Create a feature branch from `main` (e.g., `feat/my-new-feature` or `fix/issue-description`).
3.  **Local Setup**: Please refer to the **Local Development** section in the repository's `README.md` for specific setup instructions, prerequisites, and environment requirements.

## Development Standards

### General Rules
*   **Focused Changes**: Keep your Pull Requests focused. Split unrelated changes into separate PRs.
*   **Sign-off Commits**: We use the [Developer Certificate of Origin (DCO)](https://developercertificate.org/). Always sign off your commits with `git commit -s`.
*   **Tests**: Every bug fix or new feature should include corresponding tests (Unit tests or Integration tests).
*   **Documentation**: Update `README.md` or files in the `docs/` directory if your changes affect setup, configuration, or user-facing behavior.

### Repository-Specific Instructions
Some repositories have specific local development scripts or environment requirements (e.g., `scripts/dev.sh`). Always check the local `README.md` or a `DEVELOPMENT.md` if present for specialized instructions.

## Checks and Validation

To maintain high code quality, we require all contributions to pass a set of automated checks before they can be merged. Before submitting a Pull Request, please ensure the following:

*   **Code Formatting**: Your code must adhere to the project's formatting standards. 
*   **Testing**: All existing and new tests must pass locally.
*   **Verification**: The project must build successfully without errors.

Please refer to the **Local Development** or **Checks** section in the repository's `README.md` for the specific commands used to format and verify the codebase locally.

## Commit Guidelines

We strictly follow [Conventional Commits](https://www.conventionalcommits.org/). This allows us to automate our release process and generate clear changelogs.

**Format:**
```
<type>(optional scope): <description>

[optional body]

Signed-off-by: Your Name <your.email@example.com>
```

For detailed information on commit types and how they affect releases, please refer to our central [Release Process and Commit Guidelines](https://github.com/ba-itsys/actions/blob/main/docs/release-process.md).

## Pull Request Process

1.  **Rebase on Main**: Ensure your branch is rebased on the latest `main` branch. We prefer a clean, linear history.
2.  **Open PR**: Open your Pull Request against the `main` branch.
3.  **Address Feedback**: Monitor your PR for comments from reviewers. We aim for a constructive and timely review process.
4.  **Automated Releases**: Once merged, our CI/CD (release-please) will automatically track your changes for the next release.

## License

By contributing, you agree that your contributions will be licensed under the **Apache License, Version 2.0**.
