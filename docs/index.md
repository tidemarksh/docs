# Tidemark

Tidemark is a browser-hosted RISC-V Linux userland environment.

The public implementation is split across four repositories:

- [kernel](https://github.com/tidemarksh/kernel): a Rust/WebAssembly RISC-V Linux userland kernel.
- [runtime](https://github.com/tidemarksh/runtime): a TypeScript runtime that runs guest processes in workers and connects them to browser host services.
- [sdk](https://github.com/tidemarksh/sdk): a higher-level TypeScript API built on the runtime.
- [artifacts](https://github.com/tidemarksh/artifacts): build recipes and GitHub Release assets for redistributable guest payloads.

The demo application is intentionally separate from this documentation site.
This site explains repository boundaries, current implementation structure, and
the contracts between layers.

## What Tidemark Is

Tidemark is not a full virtual machine monitor and it is not a browser operating
system. The current implementation focuses on running RISC-V Linux userland
programs inside WebAssembly and browser worker infrastructure.

At a high level:

```text
application or demo
        |
        v
sdk -------------- artifact and package providers
        |
        v
runtime ---------- browser workers, process orchestration, host bridges
        |
        v
kernel ----------- RISC-V CPU, ELF loading, memory, filesystem, syscalls
        |
        v
guest program ---- RISC-V Linux userland binary
```

## Current Scope

The repositories currently contain implementation code for:

- RISC-V instruction execution and ELF loading in the kernel.
- Linux userland syscall, filesystem, process, thread, signal, pipe, socket,
  and time-related modules in the kernel.
- Runtime worker orchestration, process lifecycle handling, file snapshots,
  SharedArrayBuffer page cache support, network bridge interfaces, and debug
  helpers.
- SDK helpers for creating a runtime, adding files, running commands, applying
  artifact layers, resolving package providers, and attaching a simple terminal.
- Artifact recipes and release automation for guest runtimes, toolchains, and
  support payloads.

## License

The public source repositories are licensed under Apache-2.0 unless a repository
states otherwise. Artifact payloads keep their upstream licenses and are
distributed with release metadata and license materials.
