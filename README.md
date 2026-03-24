# <3 dating

The base dating pool for [openpool](https://github.com/vutran1710/openpool) — terminal-native, decentralized dating for developers.

## Join

```bash
# Install openpool CLI
curl -fsSL https://github.com/vutran1710/openpool/releases/latest/download/op-darwin-arm64 -o op
chmod +x op && sudo mv op /usr/local/bin/

# Launch and follow the onboarding
op
```

## Pool Config

See [pool.yaml](pool.yaml) for profile attributes, roles, and discovery settings.

## For Operators

Secrets required in GitHub Actions:

| Secret | Purpose |
|--------|---------|
| `OPERATOR_PRIVATE_KEY` | ed25519 private key (128 hex) |
| `POOL_SALT` | Secret salt for hash chain |
