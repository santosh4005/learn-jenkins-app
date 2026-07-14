# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A minimal Create React App project used as the sample application for a Jenkins CI/CD course ("Learn Jenkins on Udemy"). The app itself is intentionally trivial (a single-page CRA starter displaying a hardcoded "Application version" string); the real focus of this repo is exercising a CI pipeline (build → unit test → e2e test) rather than application features. There is no Jenkinsfile checked into the repo — the Jenkins pipeline configuration lives outside this checkout (in the Jenkins server/job config), so don't expect to find it here.

## Commands

```bash
npm start                    # dev server at http://localhost:3000
npm run build                # production build to /build
npm test                     # Jest unit tests via react-scripts, outputs JUnit XML to test-results/junit.xml
npx playwright test          # run e2e tests (e2e/*.spec.js) against baseURL
npx playwright test e2e/app.spec.js   # run a single e2e spec
npx playwright test -g "has title"    # run a single e2e test by name
```

Notes:
- `npm test` runs in CI mode via `--testResultsProcessor="jest-junit"`; it is configured to not hang waiting for watch mode (see `jest-junit` config in package.json).
- Playwright's `baseURL` defaults to `http://localhost:3000` but is overridden by the `CI_ENVIRONMENT_URL` env var in CI, since Jenkins deploys the build to a URL before running e2e tests. Set this env var when testing against a deployed environment.
- Playwright reporters are `html` and `junit`; there's no `webServer` block configured, so the app must already be running/deployed at `baseURL` before running e2e tests.
- The e2e spec checks a page title, a "Learn Jenkins on Udemy" link, and an "Application version: X" string driven by the `REACT_APP_VERSION` env var (defaults to `1` if unset) — when bumping the app version for a pipeline exercise, update both `src/App.js` and expect `REACT_APP_VERSION` to be set correctly by the pipeline.

## Architecture

- `src/App.js` — the entire application UI (no routing, no components beyond this one file).
- `e2e/app.spec.js` — Playwright end-to-end tests; `tests-examples/` contains Playwright's generated example specs (not real tests, left over from scaffolding).
- `.devcontainer/devcontainer.json` — Node 18 dev container definition for consistent local/CI environments.
- `.github/lockdown.yml` — auto-locks issues/PRs; not a CI workflow.

Since this is a CRA project, build/test tooling (webpack, Babel, ESLint via `react-app`/`react-app/jest` configs) is managed through `react-scripts` and not exposed for direct configuration unless ejected.
