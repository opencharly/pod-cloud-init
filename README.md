# pod-cloud-init

The `cloud-init` candy of the OpenCharly candy library, as a standalone repo (the
candy de-submodule cutover, kind-prefixed naming). It installs cloud-init for
first-boot VM / cloud-instance provisioning with the NoCloud datasource.

## What it provides

Installs `cloud-init` and the `growpart` partition-resize utility, then pins the
datasource to NoCloud via a drop-in at `/etc/cloud/cloud.cfg.d/99-nocloud.cfg` so
a bootc/VM guest reads its seed ISO (`user-data` / `meta-data`) at first boot.

| Property | Value |
|---|---|
| Package | `cloud-init` (+ `cloud-guest-utils` / `cloud-utils-growpart` per distro) |
| Services | `cloud-init-local`, `cloud-init`, `cloud-config`, `cloud-final` |
| Requires | `pod-sshd` |
| Drop-in | `/etc/cloud/cloud.cfg.d/99-nocloud.cfg` (`datasource_list: [ NoCloud, None ]`) |

This is the **guest-side** half. The **host-side** companion is the `RenderCloudInit`
path in the charly binary, which produces the NoCloud seed ISO from a
`kind: vm` entity's structured `CloudInit` block.

## How to use it

Compose the candy into a bootc image that wants cloud-init provisioning:

```yaml
my-cloud-image:
  candy:
    base: "quay.io/fedora/fedora-bootc:43"
    bootc: true
    candy:
      - '@github.com/opencharly/pod-cloud-init:<tag>'
```

For `source.kind: cloud_image` VMs, cloud-init typically comes pre-installed in
the upstream qcow2 and this candy is not needed.

## Layout

- `charly.yml` — the `cloud-init` candy entity plus its `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-distros:cloud-init` — the candy's properties, usage, and
  the host-side pairing.
- `/charly-internals:cloud-init-renderer` — the host-side renderer producing the
  NoCloud seed ISO.
- `/charly-vm:vms-catalog` / `/charly-vm:vm` — `kind: vm` authoring and lifecycle.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
