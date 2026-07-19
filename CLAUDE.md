# rambam-landing

Landing page(s) for the Rambam open-day event. **Static HTML in production:**
Vercel serves the `deploy/` folder, and a GitHub Action triggers a deploy on
**every push to `main`** — so `main` is the live site, not just a branch.
Layout and editing pointers: README.md. Future LP-manager spec (separate
effort, WordPress/Cloudways): `docs/ADMIN-SPEC.md`.

## Collaboration — read before changing anything

This is a **shared repo** (owner: `suppram`; collaborator: `techRambam`).
Agent sessions must:

- **Never commit or push to `main`.** Work on a feature branch, open a PR, and
  let a human merge it. Enforced locally by `.githooks/` (pre-commit +
  pre-push) once activated — the `SessionStart` hook in
  `.claude/settings.json` activates them per-clone; verify with
  `git config core.hooksPath` → `.githooks`.
- **Expect concurrent edits by the other collaborator.** `git fetch` before
  branching; never force-push shared branches; surface conflicts rather than
  overriding the other side's changes.
- **Touching `deploy/` is touching production.** The merge of a PR that edits
  `deploy/` goes live within minutes. `deploy/index.html` is the **single
  source** of the live page — do not create copies of it under `src/`
  (`src/landing/variant-1-brand.html` is a separate design draft, not a copy).
- **Never merge a PR yourself.** Merging to `main` deploys; the permission
  allowlist deliberately covers only read-only `gh pr` subcommands, so a merge
  always requires an explicit human approval.

## Guardrails

- **No secrets in git** — this static site needs no local secrets; the Vercel
  deploy-hook URL lives only in GitHub Actions secrets
  (`VERCEL_DEPLOY_HOOK_URL`). `.githooks/secret-scan` blocks credential shapes
  and dotenv files at commit time in activated clones.
- Hooks are **per-clone**: nothing changes for a clone that never activates
  them. Claude Code sessions activate them automatically on start, and the
  setting **persists for that clone** afterwards (also gating plain-terminal
  git there) — disclosed in `.githooks/README.md`. The `.gitignore` secrets
  patterns, unlike the hooks, apply to every clone.

## Environment notes (machine-specific, harmless elsewhere)

On the WSL2 dev machine this clone lives at `~/dev/rambam-landing` (launcher:
`landing`). Hebrew/RTL content: verify rendered output visually before
delivering page changes.
