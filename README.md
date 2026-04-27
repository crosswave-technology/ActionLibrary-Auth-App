# ActionLibrary-Auth-App

Composite action for GitHub App authentication. Generates a short-lived installation
token using a manually crafted JWT, without depending on third-party actions.

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

After this step, `GH_TOKEN` and `GITHUB_TOKEN` are also set in the workflow environment
for `gh` CLI and API calls.

## Inputs

| Name | Required | Description |
|------|----------|-------------|
| `app-id` | Yes | GitHub App ID |
| `app-installation-id` | Yes | App installation ID for the target org |
| `app-private-key` | Yes | RSA private key (PEM format) |

## Outputs

| Name | Description |
|------|-------------|
| `token` | Short-lived installation token (~10 min validity) |

## Security

- Token is automatically masked (`::add-mask::`) before being written to outputs
- PEM key written to a temp file with `trap` cleanup on exit
- No third-party action dependencies — pure OpenSSL + curl
