# Persistent Tools in Zeabur / K3s Deployments

When OpenClaw runs as a Helm chart on Zeabur (or any K3s cluster), it runs inside a Pod where **only `~/.openclaw` is persisted** to a PVC. Everything else is ephemeral and lost on Pod restart or upgrade.

## The Golden Rule

> Install all CLI tools and binaries under `$WORKSPACE/bin/`, not system paths.

`$WORKSPACE` defaults to `/home/node/.openclaw/workspace`.

## Why

- The Pod has **no `sudo`** and no `apt-get`
- System paths (`/usr/local/bin`, `/usr/bin`, etc.) are reset on every restart
- Only `~/.openclaw/**` survives restarts and upgrades via PVC

## Installing Tools

Instead of `apt install`, ask OpenClaw to **download the official binary directly into `$WORKSPACE/bin/`**.

Examples:

```
# GitHub CLI
$WORKSPACE/bin/gh

# Cloudflare Wrangler
$WORKSPACE/bin/wrangler

# Weather CLI
$WORKSPACE/bin/weather
```

### Example prompt to OpenClaw

> Download the latest GitHub CLI Linux amd64 binary and install it to `$WORKSPACE/bin/gh`

OpenClaw will fetch the release tarball, extract the binary, and place it at the correct path.

## Using Installed Tools

Ensure `$WORKSPACE/bin` is on `PATH` before running:

```bash
export PATH=$WORKSPACE/bin:$PATH
gh --version
```

You can also ask OpenClaw to prepend this in any command it runs, or add it to a skill's instructions.

## npm / Node Packages

For npm-based CLIs, install to the workspace as well:

```bash
npm install -g --prefix $WORKSPACE <package>
# binary lands at $WORKSPACE/bin/<package>
```

## Summary

| ✅ Persisted | ❌ Not Persisted |
|---|---|
| `~/.openclaw/**` | `/usr/local/bin/` |
| `$WORKSPACE/bin/` | `/usr/bin/` |
| `$WORKSPACE/node_modules/` | System packages (`apt`) |

As long as binaries live under `~/.openclaw`, they survive Pod restarts and Helm upgrades.
