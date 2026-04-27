# Tech Doc — ActionLibrary-Auth-App

## Design

The action manually constructs a GitHub App JWT using OpenSSL:

1. **Header**: `{"alg":"RS256","typ":"JWT"}` base64url-encoded
2. **Payload**: `{"iat":<now>,"exp":<now+540>,"iss":"<APP_ID>"}` base64url-encoded
3. **Signature**: RS256 HMAC of `header.payload` signed with the App private key
4. Exchange JWT → `POST /app/installations/{id}/access_tokens` → installation token

## Why not `actions/create-github-app-token@v1`?

- Reduces third-party action dependency surface
- Full control over JWT construction and error handling
- Single place to audit the auth logic

## Token lifetime

Tokens expire after ~10 minutes (GitHub maximum). The action requests a 9-minute
window (`exp = iat + 540`) to leave margin for clock skew.
