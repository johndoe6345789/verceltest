# GitHub Workflows

This directory contains GitHub Actions workflows copied and adapted from [primary_next_template](https://github.com/johndoe6345789/primary_next_template).

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

### checkly.yml
**Trigger:** Deployment status events

Runs E2E tests on Checkly after successful deployments:
- Only runs after successful deployments
- Tests the deployed application
- Creates test summary reports
- Deploys checks to production environment

**Note:** Requires Checkly secrets to be configured:
- `CHECKLY_API_KEY`
- `CHECKLY_ACCOUNT_ID`
- `VERCEL_BYPASS_TOKEN`

### crowdin.yml
**Trigger:** Push to main branch, Daily at 5am UTC, Manual workflow dispatch

Synchronizes translations with Crowdin:
- Uploads source files to Crowdin
- Downloads translations from Crowdin
- Creates pull requests with new translations

**Note:** Requires Crowdin secrets to be configured:
- `CROWDIN_PROJECT_ID`
- `CROWDIN_PERSONAL_TOKEN`

### release.yml
**Trigger:** After successful CI workflow completion on main branch

Creates semantic releases:
- Runs after CI workflow succeeds
- Verifies dependency integrity with `pnpm audit signatures`
- Creates GitHub releases using semantic-release

**Note:** Release configuration should be added to `package.json` if semantic releases are desired.

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
