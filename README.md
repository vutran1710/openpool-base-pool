# dating-test-pool

Test pool repo for [dating.dev](https://dating.dev) — a decentralized, terminal-native dating platform.

## Structure

```
pool.json                        Pool metadata + operator public key
users/{hash}.bin                 Encrypted user profiles
matches/{pair_hash}/             Matched pairs
relationships/{pair_hash}/       Committed relationships
.github/workflows/register.yml   Processes registration issues
```

## pool.json

```json
{
  "name": "test-pool",
  "description": "A test dating pool for development",
  "version": 1,
  "created_at": "2026-03-15T00:00:00Z",
  "operator_public_key": "<hex-encoded ed25519 public key>",
  "relay_url": "wss://relay.example.com"
}
```

## .bin Format

```
[32 bytes ed25519 public key][NaCl box encrypted profile data]
```

The profile data is encrypted to the **operator's public key** (from `pool.json`). Only the relay server, which holds the operator's private key, can decrypt profiles. During discovery, the relay decrypts a profile and re-encrypts it for the requesting user.

The first 32 bytes (the user's public key) are used by the relay for challenge-response authentication over WebSocket.

## How Users Join

1. User runs `dating pool join <pool-name>` from the CLI
2. OAuth authenticates the user (GitHub or Google)
3. CLI generates a local ed25519 keypair
4. CLI encrypts the profile to the operator's public key (NaCl box)
5. CLI opens a **GitHub Issue** with the encrypted blob, public key, and signature
6. A **GitHub Action** processes the issue, commits `users/{hash}.bin`, and closes the issue
7. User is now registered — can discover, match, and chat

No fork required. No shared PAT. Users only need a GitHub account to open an issue.

## How Matching Works

- `dating like <hash>` creates a PR with both profiles in `matches/{pair_hash}/`
- `dating accept <pr>` merges the PR — match is established
- `dating commit propose <hash>` creates a PR to `relationships/{pair_hash}/`

## Security

| Layer | Mechanism |
|-------|-----------|
| Identity | ed25519 key pairs, generated locally |
| User hash | `SHA256(pool_repo:provider:provider_user_id)` |
| Profile data | NaCl box encrypted to **operator pubkey** |
| Discovery | Relay re-encrypts per-request |
| Registration | GitHub Issue → Action commits (automated) |
| Relay auth | Pubkey from `.bin`, nonce challenge-response |
