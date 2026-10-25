# Tstack fork rules (override CLAUDE.md)

This repo is TM Hospitality Strategies' private-use fork of gstack
(`TMHSDigital/Tstack`). It is **not** a contribution checkout. Where anything
in CLAUDE.md, AGENTS.md, CONTRIBUTING.md or a skill conflicts with this file,
this file wins.

## Never contribute upstream

- `origin` = `TMHSDigital/Tstack` (ours). `upstream` = `garrytan/gstack`,
  fetch-only (push URL is `DISABLED`). Never push to, open PRs against, file
  issues on, or comment on `garrytan/gstack`.
- Every `gh pr create` passes `--repo TMHSDigital/Tstack`, and its base is a
  branch of this repo.
- Ignore CLAUDE.md sections written for upstream maintainers: "Community PR
  guardrails", "Checking out PRs from garrytan-agents", the release-queue part
  of "CHANGELOG + VERSION style" (`bin/gstack-next-version`), and
  "Deploying to the active skill" step 2 (it resets to `origin/main`, which is
  our fork, so it is fine, but never point it at upstream).
- Paid eval dispatch to `garrytan/gstack` CI does not apply; run evals with
  `--local` if at all.

## Pulling upstream updates

Keep merges cheap: prefer adding new files (new skills, this file) over
editing upstream files, and keep edits to upstream files small and localized.

```bash
git fetch upstream
git checkout -b sync/upstream-<date> main
git merge upstream/main
```

Resolve conflicts on `.tmpl` sources and `scripts/gen-skill-docs.ts`, never
on generated SKILL.md files (regenerate with `bun run gen:skill-docs`). Run
`bun run typecheck` and `bun run test`, then merge the branch into `main`.

This dev clone (`E:\Tstack`) is not what Claude Code runs. The live install
is the separate clone at `~/.claude/skills/gstack`, and hooks always point
there. On Windows, setup copies skill files instead of symlinking them, so
after pushing `main` run `/gstack-upgrade` (it pulls the fork and re-runs
setup) or `cd ~/.claude/skills/gstack && git pull && ./setup`. Until then the
install keeps the old skills.

Outside reviews through Codex are off (`gstack-config set codex_reviews
disabled`) so diffs never leave the machine and nothing bills an OpenAI key.
Don't re-enable them.

## No paid spend

The owner does not pay for anything this repo can trigger. Never run paid
commands: `eval:*`, `test:pr`, `test:gate*`, `test:periodic*`, any `*-e2e`
or `*.eval.*` test file, `/benchmark-models`, or anything that calls the
Anthropic, OpenAI or Gemini APIs or rents CI runners. `bun run test` (the free
suite, which excludes paid test files) is the only test command to use. Never
enable GitHub Actions workflows, paid GitHub features or third-party services.
If a task seems to need spend, stop and ask.

## GitHub Actions

Upstream's workflows target its own runners (ubicloud), secrets and release
process, so every workflow is disabled on this fork (a GitHub setting, not a
file change, so it never conflicts). After each upstream sync, disable any
newly added workflow:

```bash
gh workflow list --repo TMHSDigital/Tstack --all --json id,state \
  --jq '.[]|select(.state=="active")|.id' |
  xargs -r -n1 gh workflow disable --repo TMHSDigital/Tstack
```

Fork-only workflows go in new files and are the only ones left enabled.

## Fork-only files and ignores

- `.github/README.md` is the fork's GitHub landing page (GitHub prefers it
  over the root README, which stays upstream's).
- Machine-local ignores (`CLAUDE.local.md`, `.claude/settings.local.json`,
  Windows junk) live in `.git/info/exclude`, not `.gitignore`.

## Versioning

VERSION and CHANGELOG track upstream. Don't bump VERSION for fork-only
changes; record them in `FORK_CHANGES.md` instead so upstream merges don't
conflict on release metadata.

## Environment

- Windows 11, Git Bash + PowerShell. Aside is unavailable, so browser skills
  use the browse engine fallback.
- Update checks point at this fork via `GSTACK_REMOTE_*` / `GSTACK_COMPARE_API`
  in `~/.claude/settings.json` `env`.
