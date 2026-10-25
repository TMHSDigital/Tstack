# Tstack

TM Hospitality Strategies' personal fork of [gstack](https://github.com/garrytan/gstack),
tailored to our own environments. It is not a contribution fork: changes here
are not sent upstream.

- Upstream's full README: [README.md](../README.md)
- Fork rules and the upstream-sync workflow: [FORK.md](../FORK.md)
- What this fork changes on top of upstream: [FORK_CHANGES.md](../FORK_CHANGES.md)

## Install

```bash
git clone https://github.com/TMHSDigital/Tstack.git ~/.claude/skills/gstack
cd ~/.claude/skills/gstack && ./setup
```

Requires Bun 1.4.2 or newer. Re-run `./setup` (or `/gstack-upgrade`) after
pulling: Windows installs copy skill files rather than symlinking them.

Licensed MIT, as upstream; see [LICENSE](../LICENSE).
