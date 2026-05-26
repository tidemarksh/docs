# Status

This page describes the public repository state reflected by the current source
trees.

## Kernel

- Apache-2.0 licensed.
- Rust Cargo workspace.
- Kernel crate includes CPU, ELF, memory, filesystem, syscall, network, JIT,
  exports, tracepoint, and harness modules.
- Test workspace includes RISC-V and LTP-related test support.

## Runtime

- Apache-2.0 licensed.
- TypeScript package with current version `0.0.0`.
- Current package name in `package.json` is `runtime`.
- Provides worker-based process orchestration, filesystem operations, network
  bridge interfaces, and debug helpers.
- Contains workload tests and support harnesses.

## SDK

- Apache-2.0 licensed.
- TypeScript package with current version `0.0.0`.
- Current package name in `package.json` is `@tidemarksh/sdk`.
- Provides high-level file, process, artifact, package provider, terminal, and
  network helper APIs.

## Artifacts

- Apache-2.0 licensed for repository source code.
- Release payloads retain their upstream licenses.
- GitHub Actions builds and publishes artifact release assets.
- Recipes and release validation scripts are present in the repository.

## Docs

- This repository is public documentation for the architecture and repository
  boundaries.
- The demo application is maintained separately.
