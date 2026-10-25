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

## Versioning

VERSION and CHANGELOG track upstream. Don't bump VERSION for fork-only
changes; record them in `FORK_CHANGES.md` instead so upstream merges don't
conflict on release metadata.

## Environment

- Windows 11, Git Bash + PowerShell. Aside is unavailable, so browser skills
  use the browse engine fallback.
- Update checks point at this fork via `GSTACK_REMOTE_*` / `GSTACK_COMPARE_API`
  in `~/.claude/settings.json` `env`.
