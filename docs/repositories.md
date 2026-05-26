# Repositories

Tidemark is organized as separate repositories under the
[`tidemarksh`](https://github.com/tidemarksh) organization.

## Repository Map

| Repository | Primary language | Current role |
|---|---|---|
| [kernel](https://github.com/tidemarksh/kernel) | Rust | RISC-V Linux userland kernel compiled for WebAssembly targets. |
| [runtime](https://github.com/tidemarksh/runtime) | TypeScript | Browser worker runtime that runs guest processes on top of the kernel. |
| [sdk](https://github.com/tidemarksh/sdk) | TypeScript | Higher-level API for applications using the runtime. |
| [artifacts](https://github.com/tidemarksh/artifacts) | JavaScript recipes | Artifact build recipes, release automation, manifests, checksums, and license bundles. |
| [docs](https://github.com/tidemarksh/docs) | Markdown | Public architecture documentation. |

## Dependency Direction

```text
sdk
 |
 v
runtime
 |
 v
kernel

artifacts -> consumed by SDK/provider flows and tests
docs      -> explains the public architecture
demo      -> separate application layer
```

The current SDK package depends on the local runtime package in the workspace.
The runtime expects kernel WebAssembly bytes at creation time.

## What Belongs Where

Kernel:

- RISC-V CPU execution.
- ELF loading.
- Guest memory access.
- Linux userland syscall semantics.
- Filesystem, process, signal, pipe, socket, time, and thread primitives.

Runtime:

- Worker lifecycle and message routing.
- Process creation and destruction.
- Kernel worker and thread worker orchestration.
- Runtime filesystem snapshots and page cache bridge.
- Stdio, PTY mode, network bridge interfaces, and debug helpers.

SDK:

- `Tidemark.create`.
- File and directory helpers.
- Command lookup and `run` / `spawn`.
- Artifact layer installation.
- Package provider abstraction.
- Network policy and proxy helper exports.

Artifacts:

- Recipe validation.
- Artifact builds in GitHub Actions.
- Release assets with payloads, manifests, provenance, checksums, and license
  material.

Docs:

- Public explanation of the current repository layout and architecture.
- No generated payloads, private work logs, or local-only reference caches.
