# rambam-landing

Landing page(s) for the Rambam open-day event. **Static HTML in production:**
Vercel serves the `deploy/` folder, and a GitHub Action triggers a deploy on
**every push to `main`**, so `main` is the live site, not just a branch.
Layout and editing pointers: README.md. Future LP-manager spec (separate
effort, WordPress/Cloudways): `docs/ADMIN-SPEC.md`.

## Collaboration

This is a **shared repo** (owner: `suppram`; collaborator: `techRambam`).

- **Never commit or push to `main`.** Work on a feature branch and open a PR.
  `.githooks/` (pre-commit and pre-push) enforces this once activated. The
  `SessionStart` hook in `.claude/settings.json` activates them per clone;
  verify with `git config core.hooksPath`, which prints `.githooks`.
- **Expect concurrent edits by the other collaborator.** Surface conflicts
  rather than overriding the other side's changes.
- **Touching `deploy/` is touching production.** The merge of a PR that edits
  `deploy/` goes live within minutes. `deploy/index.html` is the **single
  source** of the live page. Do not create copies of it under `src/`
  (`src/landing/variant-1-brand.html` is a separate design draft, not a copy).
- **Never merge a PR yourself.** Merging to `main` deploys, so a human merges.

## Guardrails

- **No secrets in git.** This static site needs no local secrets. The Vercel
  deploy-hook URL lives only in GitHub Actions secrets
  (`VERCEL_DEPLOY_HOOK_URL`). `.githooks/secret-scan` blocks credential shapes
  and dotenv files at commit time in activated clones.
- Hooks are **per clone**: nothing changes for a clone that never activates
  them. Claude Code sessions activate them on start, and the setting persists
  for that clone afterwards (it also gates plain-terminal git there), as
  disclosed in `.githooks/README.md`. The `.gitignore` secrets patterns apply
  to every clone.

## Content

Hebrew/RTL content: verify rendered output visually before delivering page
changes.
