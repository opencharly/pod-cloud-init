# AGENTS.md — pod-cloud-init

Standalone candy repo for the `cloud-init` candy — guest-side cloud-init with the
NoCloud datasource for first-boot VM / cloud-instance provisioning. The entire
candy lives in `charly.yml` at the repo root: the `cloud-init:` entity with its
`require`, `package`, per-distro `distro:` sections, the four `service:`
entries, the plan (the NoCloud drop-in), and its `skill:` entity.

Canonical files:

- `charly.yml` — the `cloud-init` candy entity (description, `require`,
  `package`, `distro`, `service`, `plan`) and its `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-distros:cloud-init` — the owning skill: candy properties, usage, and
  the host-side pairing. Load before editing, building, deploying, or
  troubleshooting this candy.
- `/charly-internals:cloud-init-renderer` — the host-side renderer producing the
  NoCloud seed ISO this guest-side candy reads.
- `/charly-vm:vm` / `/charly-vm:vms-catalog` — `kind: vm` lifecycle and authoring.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs, per-distro sections, service declarations).
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The live R10 witness is a bootc VM's first-boot, which exercises the guest-side
  cloud-init reading the host-rendered NoCloud seed ISO. The candy's own `check:`
  steps assert the binary, the drop-in, and the growpart utility.

## Modify this repo

- Edit the `cloud-init:` candy entity in `charly.yml`; the `skill:` entity in the
  same file is the owning skill's source — a candy change and its skill change
  land together.
- Keep the four `service:` entries and the per-distro package map in step; the
  drop-in path is the contract with the host-side renderer.
- The `skill:` entity is the source for `/charly-distros:cloud-init`; never edit
  the generated `SKILL.md` in the marketplace corpus — regenerate it.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
