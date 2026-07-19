# Git guardrails (`.githooks/`)

Local git hooks for this repo. Pure POSIX `sh` — no dependencies — adapted from
sibling repos (`rambam-make-watchdog` ← `rambam-clinic`).

**This is a collaborative repo, and these hooks are strictly opt-in per clone**
(see Activation below). Nothing here changes anyone else's workflow: a clone
that never sets `core.hooksPath` behaves exactly as before. They exist because
**a push to `main` is an immediate production deploy** — the GitHub Action
calls the Vercel deploy hook on every push to `main` — so agent (Claude Code)
sessions on collaborators' machines route everything through a PR instead.

## What they enforce (in an activated clone)

| Hook | Rule | Why |
|---|---|---|
| `pre-commit` | No commits directly on `main` | `main` = the live site; work goes on feature branches, lands via PR. |
| `pre-commit` → `secret-scan` | No secret/credential in a commit | The Vercel deploy-hook URL lives only in GitHub Actions secrets; this static site needs no local secrets at all. Stops a pasted key **before** it enters history. |
| `pre-push` | No direct pushes to `main` (incl. force-push/deletion) | Direct push = instant deploy. Everything reaches `main` through a Pull Request. |

All hooks print the fix when they fire and honor `--no-verify` for genuine
emergencies.

`secret-scan` checks the staged diff for high-confidence shapes (Vercel
deploy-hook URLs, Anthropic tokens, JWTs, PEM keys), a generic
"secret-named variable = long quoted literal" pass, and any staged `.env` file.

## Activation — opt-in, once per clone

`core.hooksPath` is local git config (never committed), so a clone only runs
these hooks after:

```sh
git config core.hooksPath .githooks
```

Claude Code sessions activate it automatically (`.claude/settings.json` runs
the line above on `SessionStart`). Everyone else: entirely your choice.
