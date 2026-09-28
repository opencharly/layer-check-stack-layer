# AGENTS.md — layer-check-stack-layer

Standalone candy repo for the `check-stack-layer` layer — the second (and final)
layer of the `check-pod` R10 bed, proving layer-composition order, `kind: pod`
runtime, and deploy-target rendering. The candy lives in `charly.yml` at the
repo root: distro packages, two supervisord `service:` entries, the `port:`
declaration, and the `plan:` steps. The repo declares **no `skill:` entity**; the
owning guidance is the family skill `/charly-check:check` (the gap is tracked in
`opencharly/opencharly#291`).

Canonical files:

- `charly.yml` — the `check-stack-layer:` candy entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-check:check` — the owning (family) skill. The check bed and `plan:`
  authoring reference, incl. `check:` step verbs, the `disposable: true` bed
  model, and the probe verbs. Load before editing or troubleshooting this layer.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, per-distro `distro:` arms, package/repo
  sections, and service declarations). Load before editing any entity field or
  plan step.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- The candy's `plan:` `check:` steps are the functional evidence; they must stay
  valid on every distro arm they run on. Scope a distro-specific check in the
  command itself — the check runner does not honour runner-level
  `exclude-distro` fields.

## Modify this repo

- Keep the marker path `/etc/check-stack-marker` dedicated so concurrently
  fanned-out beds do not collide.
- Both services are custom `exec:` entries (fedora-minimal ships no systemd units
  for `nc`/`sleep`), so this layer stays supervisord-only on container targets —
  do not convert them to packaged units.
- A box's own `run:` steps are not part of the build timeline; the act-emit
  `unix_group` step runs on the LAYER, not the box.
- New behaviour claims belong in the `plan:` as an observable `check:` step.
- This repo has no `skill:` entity, so there is no projected corpus to mirror.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
