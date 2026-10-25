# TStack rules (override CLAUDE.md and AGENTS.md)

TStack is TM Hospitality Strategies' own fork of gstack
(`TMHSDigital/Tstack`). It is **not** a contribution checkout. Where anything
in CLAUDE.md, AGENTS.md, CONTRIBUTING.md or a skill conflicts with this file,
this file wins. Code, binaries, config keys and skill names keep upstream's
"gstack" names; don't rename them (that would break upstream merges).

## Work directly on main

- Commit straight to `main` and push with `git push origin main`. Don't create
  feature branches, worktrees for changes, or pull requests, and don't run
  `gh pr create` / `gh pr merge`.
- When a skill would branch or open a PR (`/ship`, `/land-and-deploy`,
  `/spec`, `/review` base-branch logic, `/document-release`), do the work on
  `main` instead: commit there and push. Skip PR creation, PR bodies, PR
  merges and version-queue steps.
- Keep commits bisected (one logical change each), per CLAUDE.md's commit
  style.
- Before pushing, run the focused checks for what changed (and
  `bun run typecheck` for code); `main` is the only line of history, so a bad
  push lands for real. Fix forward with a new commit; never force-push `main`.

## Never contribute upstream

- `origin` = `TMHSDigital/Tstack` (ours). `upstream` = `garrytan/gstack`,
  fetch-only (push URL is `DISABLED`). Never push to, open PRs against, file
  issues on, or comment on `garrytan/gstack`.
- Ignore CLAUDE.md sections written for upstream maintainers: "Community PR
  guardrails", "Checking out PRs from garrytan-agents", the release-queue part
  of "CHANGELOG + VERSION style" (`bin/gstack-next-version`), and
  "Deploying to the active skill" step 2 (it resets to `origin/main`, which is
  our fork, so it is fine, but never point it at upstream).
- Paid eval dispatch to `garrytan/gstack` CI does not apply.

## Pulling upstream updates

Keep merges cheap: prefer adding new files (new skills, this file) over
editing upstream files, and keep edits to upstream files small and localized.
Merge directly on `main`:

```bash
git checkout main && git pull origin main
git fetch upstream
git merge upstream/main
```

Resolve conflicts on `.tmpl` sources and `scripts/gen-skill-docs.ts`, never
on generated SKILL.md files (regenerate with `bun run gen:skill-docs`). Run
`bun run typecheck` and `bun run test:windows` before pushing. If the merge
goes wrong before it is pushed, `git merge --abort` (or reset `main` to
`origin/main`) and start over; once pushed, fix forward.

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
commands: `eval:*`, `test:pr`, `test:gate*`, `test:periodic*`, `test:release`,
any file matching `PAID_TEST_GLOBS` in `test/helpers/paid-test-set.ts`,
`/benchmark-models`, or anything that calls the Anthropic, OpenAI or Gemini
APIs or rents CI runners. The free suite excludes those files: on this
Windows machine run `bun run test:windows` (the Windows-safe subset upstream's
Windows CI uses); plain `bun run test` includes POSIX-only files that fail
here for environmental reasons. Some `test:windows` files also fail on
untouched upstream code on this machine, so compare a failure against
`upstream/main` before blaming a change. Never enable GitHub Actions
workflows, paid GitHub features or third-party services. If a task seems to
need spend, stop and ask.

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

## TStack-only files and ignores

- `FORK.md` (this file) and `FORK_CHANGES.md` (log of TStack changes).
- `.github/README.md` is TStack's GitHub landing page (GitHub prefers it over
  the root README, which stays upstream's).
- The top of CLAUDE.md and AGENTS.md carries a short TStack header; leave the
  rest of those files upstream's so merges stay clean.
- Machine-local ignores (`CLAUDE.local.md`, `.claude/settings.local.json`,
  Windows junk, `*.stackdump`) live in `.git/info/exclude`, not `.gitignore`.

## Versioning

VERSION and CHANGELOG track upstream. Don't bump VERSION for TStack-only
changes; record them in `FORK_CHANGES.md` instead so upstream merges don't
conflict on release metadata.

## Environment

- Windows 11, Git Bash + PowerShell. Aside is unavailable, so browser skills
  use the browse engine fallback.
- Update checks point at this fork via `GSTACK_REMOTE_*` / `GSTACK_COMPARE_API`
  in `~/.claude/settings.json` `env`.
