# Contributing to Fidelis CRM

We welcome contributions to the Fidelis CRM project! Whether you're fixing a bug, adding a new feature, improving documentation, or just providing feedback, your input is valuable.

Please take a moment to review this document to make the contribution process as smooth as possible.

## Table of Contents

-   [Code of Conduct](#code-of-conduct)
-   [How to Contribute](#how-to-contribute)
    -   [Reporting Bugs](#reporting-bugs)
    -   [Suggesting Enhancements](#suggesting-enhancements)
    -   [Submitting Pull Requests](#submitting-pull-requests)
-   [Development Setup](#development-setup)
-   [Monorepo Structure](#monorepo-structure)
-   [Coding Guidelines](#coding-guidelines)
    -   [TypeScript](#typescript)
    -   [Formatting and Linting](#formatting-and-linting)
    -   [Commit Messages](#commit-messages)
    -   [Testing](#testing)
-   [Further Steps](#further-steps)

## Code of Conduct

Please note that this project is released with a [Contributor Code of Conduct](CODE_OF_CONDUCT.md). By participating in this project, you agree to abide by its terms.

## How to Contribute

### Reporting Bugs

If you find a bug, please open an issue on our [GitHub Issues](https://github.com/your-username/fidelis/issues).

When reporting a bug, please include:

-   A clear and concise description of the bug.
-   Steps to reproduce the behavior.
-   Expected behavior.
-   Screenshots or error messages if applicable.
-   Your operating system and browser version.

### Suggesting Enhancements

We love new ideas! If you have a suggestion for an enhancement or a new feature:

1.  Check the existing [GitHub Issues](https://github.com/your-username/fidelis/issues) to see if your idea has already been proposed.
2.  Open a new issue with the label `enhancement`.
3.  Clearly describe the enhancement, including why you think it would be valuable to the project.

### Submitting Pull Requests

Follow these steps to contribute code:

1.  **Fork the repository:** Click the "Fork" button on the top right of the GitHub page.
2.  **Clone your fork:**
    ```bash
    git clone https://github.com/your-username/fidelis.git
    cd fidelis
    ```
3.  **Set up the development environment:** Refer to the [README.md](README.md) for instructions on setting up the Dev Container or local environment.
    ```bash
    pnpm install
    # Set up database and run migrations
    ```
4.  **Create a new branch:** Use a descriptive branch name.
    ```bash
    git checkout -b feature/your-feature-name # for features
    git checkout -b bugfix/issue-number # for bug fixes
    ```
5.  **Make your changes:** Implement your feature or fix the bug.
6.  **Write tests:** Ensure your changes are covered by appropriate tests.
7.  **Run linting and formatting checks:**
    ```bash
    pnpm lint
    pnpm format
    ```
8.  **Run tests:**
    ```bash
    pnpm test
    ```
9.  **Commit your changes:** Follow the [Commit Messages](#commit-messages) guidelines.
    ```bash
    git commit -m "feat: Add new contact form"
    ```
10. **Push your branch to your fork:**
    ```bash
    git push origin feature/your-feature-name
    ```
11. **Open a Pull Request (PR):**
    -   Go to the original Fidelis repository on GitHub.
    -   You should see a prompt to open a PR from your branch.
    -   Provide a clear title and description for your PR, referencing any related issues.
    -   Ensure all CI checks pass.

## Development Setup

Please refer to the root [README.md](README.md) for detailed instructions on setting up your development environment using Docker Dev Containers or a local setup.

## Monorepo Structure

Fidelis is a monorepo managed with Turborepo and PNPM.
-   `apps/frontend`: Next.js application (React, TypeScript, TailwindCSS)
-   `apps/backend`: Hono API (TypeScript, Prisma, Zod, OpenAPI)
-   `packages/ui`: Shared React component library (Tailwind, Radix UI)
-   `packages/utils`: Common helper and utility functions
-   `packages/types`: Shared TypeScript type definitions

## Coding Guidelines

### TypeScript

-   Always use TypeScript.
-   Explicitly define types where clarity is needed, avoid `any` when possible.
-   Utilize shared types from `packages/types`.

### Formatting and Linting

-   We use ESLint for linting and Prettier for code formatting.
-   Your code will be automatically checked in CI. Before submitting a PR, ensure your changes pass:
    ```bash
    pnpm lint
    pnpm format:check # to check formatting
    pnpm format # to automatically fix formatting issues
    ```

### Commit Messages

We follow the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) specification. This helps us generate release notes and understand the history of changes.

Examples:

-   `feat: Add new dashboard statistics widget`
-   `fix(backend): Resolve contact creation error`
-   `docs: Update contribution guide`
-   `chore: Upgrade dependencies to latest versions`
-   `refactor(frontend): Improve component reusability`

### Testing

-   All new features and bug fixes should include corresponding tests (unit, integration).
-   Run `pnpm test` to execute all tests.

## Further Steps

Once your PR is open, project maintainers will review your changes. Be prepared to address feedback and make further adjustments.

Thank you for your contribution!