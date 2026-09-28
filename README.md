# check-stack-layer

Combined layer + pod + deploy-target smoke layer for the `check-pod` R10 bed.

The `check-stack-layer` candy is the second (and final) layer of the
`check-pod` stack. In one layer it proves three mechanisms the combined
`charly check run check-pod` bed covers beyond a build smoke:

- **Layer composition order** — asserts `/etc/check-base-marker` (written by the
  prior `check-base-layer`) is still present.
- **`kind: pod` runtime** — runs `nc -lk 18794` under the configured init system
  and probes the listening port (harness + in-container `ss`).
- **Deploy-target rendering** — runs `sleep infinity` under supervisord and
  probes the service is running.

It installs `ncat` + `iproute` (port listener) + `coreutils` (`sleep`) +
`supervisor`. Both services are custom `exec:` entries — fedora-minimal ships no
systemd units for `nc`/`sleep`, so the layer is supervisord-only on container
targets.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `check-stack-layer` |
| Packages (fedora) | `iproute`, `ncat`, `coreutils`, `supervisor` |
| Port | `18794` (inherited by composing boxes and auto-published on a free host port) |
| Services | `check-listener` (`nc -lk 18794`), `check-sleep` (`sleep infinity`) |
| Marker | `/etc/check-stack-marker` |

## How to use it

Compose the layer by pinning this repo in a box's `candy:` list:

```yaml
my-bed:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/layer-check-stack-layer:v2026.241.1216'
```

The `plan:` covers the marker file, the composition-order assertion, the
listening port (runtime), the running service, an `expect_non_zero` negative
case, and a `unix_group` act-emit step that exercises state provisioning at
image build.

## Layout

- `charly.yml` — the `check-stack-layer:` candy entity: distro packages, the two
  `service:` entries, the `port:` declaration, and the `plan:` steps.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-check:check` — the check bed and `plan:` authoring
  reference (this repo declares no `skill:` entity; the gap is tracked in
  [`opencharly/opencharly#291`](https://github.com/opencharly/opencharly/issues/291))
- The second layer of the `check-pod` bed (after `check-base-layer`)
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
