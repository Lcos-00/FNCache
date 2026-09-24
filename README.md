# FNCache
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fareniya%2FFNCache.svg?type=shield)](https://app.fossa.com/projects/git%2Bgithub.com%2Fareniya%2FFNCache?ref=badge_shield)


FNCache is an experimental eBPF datapath project for accelerating selected
cross-node Pod IPv4 traffic in a Flannel VXLAN environment.

## Snapshot Status

This repository is an **M1 experimental datapath snapshot**. It is published
to preserve the current implementation and test evidence, not as a v0.5
production or pre-production release.

The current snapshot includes:

- Four TC/eBPF programs: `tc_init_e`, `tc_restore`, `tc_masq`, and `tc_init_in`.
- The v1 C ABI and fixed Map definitions.
- Control-map fail-safe checks, packet boundary validation, TOS masking, and
  ready-bit update protection.
- Helper, adjust-room, store, and redirect failure handling.
- libbpf-based ABI and BPF behavior tests.
- GitHub-hosted CI for build, ABI, verifier/load, and behavior tests.

The following are not included or claimed by this snapshot:

- Go control-plane agent, Kubernetes Informers, or Helm deployment.
- Real TC attachment and Linux netns/VXLAN integration.
- Two-node cross-node end-to-end validation.
- Performance, fuzz, capacity, upgrade, recovery, or 24-hour soak evidence.

Do not use this snapshot as a production networking component.

## Build and Test

The build requires a Linux host with Clang/LLVM, GCC, libbpf, libelf, zlib,
and a BPF-capable kernel. The commands below keep build output in a temporary
directory instead of relying on project-specific host paths:

```sh
BUILD_ROOT="$(mktemp -d)"
trap 'rm -rf "$BUILD_ROOT"' EXIT

make -C bpf \
  CLANG=clang \
  LLC=llc \
  BUILD_DIR="$BUILD_ROOT/bpf" \
  all

make -C tests \
  CC=gcc \
  CLANG=clang \
  LLC=llc \
  BUILD_DIR="$BUILD_ROOT/tests" \
  test

make -C tests \
  CC=gcc \
  CLANG=clang \
  LLC=llc \
  BUILD_DIR="$BUILD_ROOT/tests" \
  RUN="sudo -n" \
  test-bpf
```

The behavior tests require permission to load and run BPF programs. They are
also executed by `.github/workflows/ci.yml` on a GitHub-hosted Ubuntu runner.

## Repository Layout

- `bpf/`: BPF C programs and shared ABI headers.
- `tests/`: ABI layout and libbpf behavior tests.
- `.github/workflows/ci.yml`: GitHub-only M1 CI.

## License

Original contributions in this repository are provided under the Apache
License, Version 2.0. Third-party or paper-derived material, if present,
remains subject to its applicable attribution and license requirements.
See `LICENSE`.


[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fareniya%2FFNCache.svg?type=large)](https://app.fossa.com/projects/git%2Bgithub.com%2Fareniya%2FFNCache?ref=badge_large)