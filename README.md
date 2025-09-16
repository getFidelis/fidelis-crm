# Fidelis CRM Monorepo

Welcome to the Fidelis CRM project! This monorepo houses the frontend, backend, and shared packages for a modular, open-source CRM solution.

## Table of Contents

- [Project Overview](#project-overview)
- [Monorepo Structure](#monorepo-structure)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
  - [Development Container (Recommended)](#development-container-recommended)
  - [Local Setup](#local-setup)
- [Available Scripts](#available-scripts)
- [Backend (apps/backend)](#backend-appsbackend)
- [Frontend (apps/frontend)](#frontend-appsfrontend)
- [Shared Packages](#shared-packages)
- [Contributing](#contributing)
- [License](#license)

## Project Overview

Fidelis is an open-source Customer Relationship Management (CRM) system designed for simplicity and extensibility. It aims to provide essential CRM functionalities for managing contacts, organizations, and interactions.

## Monorepo Structure

-   `apps/frontend`: Next.js application (TypeScript, App Router, TailwindCSS)
-   `apps/backend`: Hono API (TypeScript, REST API, OpenAPI)
-   `packages/ui`: Shared React component library (Tailwind, Radix UI)
-   `packages/utils`: Common helper and utility functions
-   `packages/types`: Shared TypeScript type definitions

## Prerequisites

Before you begin, ensure you have the following installed:

-   [Node.js](https://nodejs.org/) (v18 or higher)
-   [PNPM](https://pnpm.io/) (v9 or higher)
-   [Docker](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/) (for dev container or local database)

## Getting Started

### Development Container (Recommended)

The easiest way to get started is by using the provided Dev Container setup. This will create a consistent development environment with all dependencies pre-configured.

1.  **Install VS Code Remote - Containers extension:** If you're using VS Code, install the "Remote - Containers" extension.
2.  **Open in Container:**
    -   Open this project in VS Code.
    -   VS Code will prompt you to "Reopen in Container". Click it.
    -   Alternatively, open the command palette (Ctrl+Shift+P or Cmd+Shift+P) and select "Remote-Containers: Reopen in Container".
3.  The Dev Container will build (this might take a few minutes the first time) and provision a PostgreSQL database.

### Local Setup

If you prefer to set up locally:

1.  **Install PNPM dependencies:**
    ```bash
    pnpm install
    ```
2.  **Database Setup:**
    -   Ensure you have a PostgreSQL database running. You can use Docker:
        ```bash
        docker compose -f .devcontainer/docker-compose.yml up -d db
        ```
    -   Copy the root `.env.example` to `.env` and fill in your database connection string and other environment variables.
        ```bash
        cp .env.example .env
        # Edit .env with your specific values
        ```
3.  **Generate Prisma Client and Run Migrations:**
    ```bash
    pnpm run --filter=backend prisma:generate
    pnpm run --filter=backend prisma:migrate:deploy
    ```
4.  **Start Development Servers:**
    ```bash
    pnpm dev
    ```
    This will start both the backend and frontend in development mode.

## Available Scripts

These scripts can be run from the root of the monorepo:

-   `pnpm dev`: Starts all `dev` scripts in parallel (backend and frontend).
-   `pnpm build`: Builds all applications and packages.
-   `pnpm lint`: Lints all applications and packages.
-   `pnpm format`: Formats all code with Prettier.
-   `pnpm format:check`: Checks if all code is formatted correctly.
-   `pnpm test`: Runs tests across all applications and packages.
-   `pnpm type-check`: Runs TypeScript type checks.
-   `pnpm clean`: Cleans build outputs and `node_modules`.

You can also run scripts for specific apps/packages using `pnpm run --filter=<package-name> <script>`. For example:
-   `pnpm run --filter=backend dev`
-   `pnpm run --filter=frontend build`

## Backend (`apps/backend`)

The backend is built with Hono.js and provides a REST API for core CRM functionalities.

-   **Technologies:** Hono, Prisma (PostgreSQL), Zod, JWT
-   **API Documentation:** Accessible via `/swagger` endpoint when the backend is running.
-   **Environment Variables:** See `apps/backend/.env.example`.

## Frontend (`apps/frontend`)

The frontend is a Next.js application using the App Router.

-   **Technologies:** Next.js, React, TypeScript, TailwindCSS, shadcn/ui
-   **Reusable Components:** Utilizes components from `packages/ui`.
-   **Environment Variables:** See `apps/frontend/.env.example`.

## Shared Packages

-   `packages/ui`: A collection of reusable React components using Tailwind CSS and Radix UI.
-   `packages/utils`: General utility functions and helpers that can be used across the monorepo.
-   `packages/types`: Shared TypeScript interfaces and types for consistent data modeling between frontend and backend.

## Contributing

We welcome contributions! Please see our [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to get involved.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.