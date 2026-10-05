# Workshop 6: DevOps in the Cloud

Repository: https://github.com/devilcalls/bitcoin-order-app

Frontend: https://devilcalls.github.io/bitcoin-order-app/

## Pipeline

Every push to `githubcicd` runs `.github/workflows/build.yml`:

1. Check out the branch and its tags.
2. Install Node 16.20.2 and Chrome, and cache npm downloads.
3. Install the locked dependencies with `npm ci`.
4. Build the production frontend with `/bitcoin-order-app/` as its base path.
5. Run ESLint on TypeScript and Angular templates.
6. Run Jasmine tests in headless Chrome. Failures stop publication.
7. Generate a conventional changelog and push a release tag when appropriate.
8. Create a GitHub release for the generated tag.
9. Publish the tested build to `gh-pages` using `npm run deploy -- --no-build`.

GitHub Pages uses **Deploy from a branch**, with `gh-pages` and `/ (root)` as the publishing source. Inherited GitHub Actions workflows were enabled on this fork before triggering the initial verification run.

## Authentication

`WORKSHOP6_GITHUB_TOKEN` is an encrypted repository Actions secret. The workflow retains the workshop's `WORKSHOP6_GITHUB_TOKEN` and `GITHUB_TOKEN` names. Deployment passes the same secret through the publisher's `GH_TOKEN` variable.

The initial setup reused the existing GitHub OAuth login credential with `repo` and `workflow` scopes. It did not generate a new personal access token. For a literal PAT exercise or if that login is revoked, generate a token with the workshop scopes and replace the secret under **Settings → Secrets and variables → Actions**. Never commit or paste the token into source files.

## Local verification

Use Node 16.20.2 (also recorded in `.nvmrc`) and a Chrome installation:

```sh
npm ci
npm run build
npm run lint
npm test
```

Set `CHROME_BIN` to the Chrome executable if Karma cannot find it. `.npmrc` applies `legacy-peer-deps` consistently because the supplied project mixes older Angular libraries. Node 16 is kept for compatibility with Angular 13; upgrading the application and runtime is separate work.

## Changes from the supplied sample

- Updated checkout, setup-node, caching, changelog, and release actions.
- Enabled the test step and registered its CI browser launcher.
- Replaced obsolete TSLint/Codelyzer with Angular-compatible ESLint and restored `ng lint`.
- Pinned compatible Node type definitions instead of installing `@types/node@latest` during CI.
- Used the already-built output for deployment so the published files are the files that were verified.
- Kept release tags on the verified source commit without creating a version-bump commit during deployment.
- Corrected the PWA manifest paths for a project hosted under `/bitcoin-order-app/`.

GitHub Pages hosts the frontend only. The supplied production API URL points to a Heroku Git remote, not a deployed API. Backend-dependent order and price operations need a separately deployed backend and a valid API URL.

The optional DockerHub/AWS deployment and destructive branch-deletion exercises are not part of this frontend Pages setup.
