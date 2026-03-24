# <3 dating test

Test pool for [dating.dev](https://dating.dev) — decentralized, terminal-native matching.

## Structure

```
pool.yaml                        Pool config (schema, roles, indexing, interest expiry)
users/{bin_hash}.bin             Encrypted profiles: [32B pubkey][NaCl box profile]
matches/{pair_hash}.json         Match files (mutual interest confirmed)
.github/workflows/
  pool-register.yml              Process registration issues
  pool-interest.yml              Process interest issues (ephemeral hash titles)
  pool-unmatch.yml               Process unmatch issues
  pool-indexer.yml               Cron: build chain-encrypted index.db
  pool-squash.yml                Cron: squash history + close stale interests
```

## pool.yaml

Defines profile attributes, roles, discovery config, and interest expiry:

```yaml
name: "<3 dating test"
relay_url: wss://relay.example.com
operator_public_key: c251e2cf...
interest_expiry: 3d

profile:
  age: { type: range, min: 18, max: 100 }
  interests: { type: multi, values: hiking, coding, music, ... }
  about: { type: text, required: false }
  phone: { type: text, visibility: private }

roles: [man, woman]

indexing:
  partitions: [{ field: role }]
  permutations: 5
  difficulty: 20
```

## Secrets (GitHub Actions)

| Secret | Purpose |
|--------|---------|
| `OPERATOR_PRIVATE_KEY` | ed25519 private key (128 hex) for decrypting profiles |
| `POOL_SALT` | Secret salt for hash chain derivation |

## How It Works

1. **Register**: User creates issue → Action validates profile against schema, commits `.bin`
2. **Discover**: Indexer builds chain-encrypted `index.db` → client grinds to unlock profiles
3. **Interest**: User creates issue with ephemeral hash title → Action detects mutual interest
4. **Chat**: Relay authenticates via TOTP, routes E2E encrypted messages
5. **Unmatch**: User creates issue → Action deletes match file
