# GitHub Workflows

This directory contains GitHub Actions workflows copied and adapted from [primary_next_template](https://github.com/johndoe6345789/primary_next_template).

## Status Overview

| Workflow | Status | Notes |
|----------|--------|-------|
| CI.yml | ✅ Ready | Works out of the box |
| nextjs.yml | ⚠️ Limited | Pre-existing, incompatible with dynamic features |
| checkly.yml | ⏸️ Requires Setup | Needs Checkly account and configuration |
| crowdin.yml | ⏸️ Requires Setup | Needs Crowdin account and configuration |
| release.yml | ⏸️ Requires Setup | Needs semantic-release configuration |

## Workflows

### CI.yml
**Trigger:** Push to main branch, Pull requests to main branch

The main continuous integration workflow that:
- Builds the Next.js application on Node.js 22.x
- Caches Next.js build output for faster subsequent builds
- Validates that the project can be built successfully

### nextjs.yml
**Trigger:** Push to main branch, Manual workflow dispatch

Deploys the Next.js site to GitHub Pages:
- Detects package manager automatically
- Builds the Next.js application for static export
- Uploads artifacts to GitHub Pages
- Deploys to GitHub Pages

**Note:** This workflow was pre-existing in the repository. However, it requires Next.js to be configured for static export by adding `output: 'export'` to `next.config.ts`. 

**Important:** This repository uses dynamic features (middleware, Redis, server-side rendering) that are **not compatible** with static export. This workflow is kept for reference but will not work without significant changes to the application architecture. Consider disabling or removing this workflow if you plan to deploy to Vercel or another platform that supports dynamic features.

### checkly.yml
**Trigger:** Deployment status events

Runs E2E tests on Checkly after successful deployments:
- Only runs after successful deployments
- Tests the deployed application
- Creates test summary reports
- Deploys checks to production environment

**Note:** This workflow is copied for reference but requires additional setup:
- Checkly secrets must be configured: `CHECKLY_API_KEY`, `CHECKLY_ACCOUNT_ID`, `VERCEL_BYPASS_TOKEN`
- Checkly configuration files must be added to the repository (e.g., `__checks__/` directory)
- `dotenv-cli` and `checkly` packages should be installed: `pnpm add -D dotenv-cli checkly`
- A production environment configuration file is needed

This workflow will not run successfully without proper Checkly setup.

### crowdin.yml
**Trigger:** Push to main branch, Daily at 5am UTC, Manual workflow dispatch

Synchronizes translations with Crowdin:
- Uploads source files to Crowdin
- Downloads translations from Crowdin
- Creates pull requests with new translations

**Note:** This workflow is copied for reference but requires additional setup:
- Crowdin secrets must be configured: `CROWDIN_PROJECT_ID`, `CROWDIN_PERSONAL_TOKEN`
- Crowdin configuration file (`crowdin.yml`) must be added to the repository root
- Localization source files must be organized according to Crowdin configuration

This workflow will not run successfully without proper Crowdin setup.

### release.yml
**Trigger:** After successful CI workflow completion on main branch

Creates semantic releases:
- Runs after CI workflow succeeds
- Verifies dependency integrity with `pnpm audit signatures`
- Creates GitHub releases using semantic-release

**Note:** This workflow requires semantic-release configuration in `package.json`. Add the following configuration to enable it:

```json
"release": {
  "branches": ["main"],
  "plugins": [
    ["@semantic-release/commit-analyzer", { "preset": "conventionalcommits" }],
    "@semantic-release/release-notes-generator",
    "@semantic-release/github"
  ]
}
```

Also install the required dependencies: `pnpm add -D semantic-release @semantic-release/commit-analyzer @semantic-release/release-notes-generator conventional-changelog-conventionalcommits`

## Custom Actions

### setup-project
A composite action that sets up the Node.js environment with pnpm:
- Installs specified Node.js version
- Installs pnpm package manager (v10.12.4)
- Caches pnpm store for faster installations
- Installs dependencies with `pnpm install --frozen-lockfile`
- Optionally restores Next.js build cache

## Adaptations from primary_next_template

The workflows have been adapted for this repository:

1. **Package Manager:** Changed from npm to pnpm
2. **Build Script:** Uses `pnpm run build` instead of `npm run build-local`
3. **Simplified CI:** Removed tests, linting, type checking, Storybook, and E2E tests that don't exist in this repository
4. **Node Version:** Using Node.js 22.x as primary version
5. **Dependencies:** Updated cache keys to use `pnpm-lock.yaml` instead of `package-lock.json`

## Build Verification

The workflows have been structured to work with the current repository setup. The CI workflow will:
1. Check out the code
2. Set up Node.js and install dependencies
3. Build the Next.js application
4. Cache the build output for subsequent jobs

## Future Enhancements

If you want to add more checks, consider:
- Adding ESLint for code linting
- Adding TypeScript type checking
- Adding unit tests with Vitest or Jest
- Adding E2E tests with Playwright
- Adding Storybook for component development
