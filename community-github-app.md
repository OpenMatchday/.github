# Open Matchday community GitHub App

Owner setup for AC-10.6 (community backend opens PRs into `teams` only).

Sources:

- Registering a GitHub App: https://docs.github.com/en/apps/creating-github-apps/registering-a-github-app/registering-a-github-app
- Installation access tokens: https://docs.github.com/en/apps/creating-github-apps/authenticating-with-a-github-app/generating-an-installation-access-token-for-a-github-app
- Private keys: https://docs.github.com/en/apps/creating-github-apps/authenticating-with-a-github-app/managing-private-keys-for-github-apps
- Permissions: https://docs.github.com/en/apps/creating-github-apps/registering-a-github-app/choosing-permissions-for-a-github-app

## Register (organization OpenMatchday)

1. GitHub -> Organization settings -> Developer settings -> GitHub Apps -> New GitHub App.
2. Name: `Open Matchday Community` (slug ideally `open-matchday-community`).
3. Homepage URL: `https://github.com/OpenMatchday/community`.
4. Webhook: Active unchecked until OM-042 needs PR status webhooks.
5. Repository permissions (minimum):
   - Contents: Read and write
   - Pull requests: Read and write
   - Metadata: Read-only (required)
6. Do not grant administration, workflows, or members permissions.
7. Where can this GitHub App be installed?: Only on this account.
8. Create the app. Note **App ID** and **Client ID**.
9. Credentials -> Key pairs -> New key. Store the PEM privately (never commit).
10. Install the app on OpenMatchday, repository access: **Only select repositories** -> `teams`.
11. Note the **Installation ID** from the installation URL (`/settings/installations/<id>`).

## Vercel Secrets (`open-matchday-community`)

Set:

- `GITHUB_APP_CLIENT_ID`
- `GITHUB_APP_ID`
- `GITHUB_APP_PRIVATE_KEY` (PEM; use `\n` for newlines if single-line)
- `GITHUB_APP_INSTALLATION_ID`
- Optional: `GITHUB_APP_SLUG=open-matchday-community`

Redeploy. `GET /health` must report `github_app_configured: true`.

## Runtime policy (enforced in community code)

- Mint installation tokens scoped to `teams` with `contents:write` + `pull_requests:write` only (expire ~1 hour).
- Create `community/contrib-*` branches only; never push `main`.
- Write only allowlisted `packages/...` paths (see `teams/docs/contribution-paths.md`).
- Commit as `{slug}[bot]` / `{app_id}+{slug}[bot]@users.noreply.github.com` (never Google email, never fabricated personal emails).
- Open PR with body crediting the chosen handle; never auto-merge.
