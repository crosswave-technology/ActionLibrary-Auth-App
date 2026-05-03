# ActionLibrary-Auth-App

Composite action for GitHub App authentication. Generates a short-lived installation
token by delegating to `actions/create-github-app-token@v1` (SHA-pinned) -- no OpenSSL
dependency, GitHub-maintained JWT generation.

## Usage

```yaml
- name: Authenticate GitHub App
  id: auth
  uses: crosswave-technology/ActionLibrary-Auth-App@v1.0.0
  with:
    app-id: ${{ secrets.GNM_APP_ID }}
    app-installation-id: ${{ secrets.GNM_APP_INSTALLATION_ID }}
    app-private-key: ${{ secrets.GNM_APP_PRIVATE_KEY }}

- name: Checkout (using App token)
  uses: actions/checkout@v4
  with:
    token: ${{ steps.auth.outputs.token }}
```

After this step, `GH_TOKEN` and `GITHUB_TOKEN` are set in the workflow environment
for `gh` CLI and API calls.

## Inputs

| Name | Required | Description |
|------|----------|-------------|
| `app-id` | Yes | GitHub App ID |
| `app-installation-id` | No | Accepted for interface compatibility -- not currently wired |
| `app-private-key` | Yes | RSA private key (PEM format) |

## Outputs

| Name | Description |
|------|-------------|
| `token` | Short-lived installation token (~1 hour validity) |

## Security

- Token is automatically masked (`::add-mask::`) before being written to outputs
- JWT generation delegated to `actions/create-github-app-token` (pinned to commit SHA)
- `GH_TOKEN` and `GITHUB_TOKEN` set via GITHUB_ENV for downstream steps