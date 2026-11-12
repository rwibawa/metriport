# Copilot instructions

## Project structure

Metriport is a TypeScript-heavy npm-workspaces monorepo for a healthcare data platform. The root
`package.json` lists the workspaces; not every directory under `packages/` is an npm workspace.

- `packages/api` contains the API server.
- `packages/core` contains shared domain, command, and integration logic.
- `packages/shared`, `packages/utils`, and the SDK packages provide shared code and client libraries.
- `packages/infra` contains AWS CDK infrastructure.
- Other `packages/` directories contain services, partner integrations, data transformations, and
  SDKs, including some implemented in Python or Java.
- `docs/` contains the developer documentation.

Before changing a package, read its README and `package.json`; package-specific scripts and setup
requirements can differ.

## Coding conventions

Follow `CONVENTIONS.md` and the conventions already used in the target package. Prefer small,
deterministic functions, immutable data, low coupling, and the Onion Pattern for package layering.
Use meaningful names: PascalCase for classes and enums, camelCase for variables and functions,
kebab-case for file names, and snake_case for database tables and columns.

For TypeScript, use explicit types, prefer `const`, avoid `any`, use `async`/`await` and strict
equality, and keep `null` at external boundaries rather than passing it through application code.
Preserve original errors as `cause`; use the project's error and logging helpers instead of
unstructured logs. Add comments to explain why, not how. Use ESLint and Prettier; the root Prettier
configuration uses a 100-character print width.

## Build, test, and run

Use Node.js 18 (CI uses 18.14) and npm. From the repository root, initialize dependencies once and
build all workspaces:

```sh
npm run init
npm run build
```

The root `npm run build:cloud` is the CI/cloud build. For validation, use `npm run lint`,
`npm run typecheck`, and `npm run test`. The root test script runs workspace tests using GNU
Parallel, so install it if that command reports it is missing. TypeScript packages generally use
Jest with ts-jest; check the target package's scripts and test configuration because not all
packages use the same test tooling.

Run tests for one workspace with `npm run test -w <workspace>` (for example,
`npm run test -w api`). Integration tests are package-specific; `npm run test:e2e` runs available
workspace E2E scripts and may require additional services, environment variables, or credentials.

To run the API locally, follow the setup in `README.md`: configure the required local environment,
start its Docker Compose dependencies from `packages/api` with `npm run start-dependencies`, then
run `npm run dev` in that package. Docker and the required local services must be available.
Never add secrets or real patient data to the repository; use local test configuration and
synthetic data.
