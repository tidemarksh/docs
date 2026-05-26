# Architecture

Tidemark uses a layered architecture. Each repository owns a different part of
the system rather than mirroring the same directory tree across languages.

```text
+-----------------------------+
| user app, integration, demo |
+-------------+---------------+
              |
              v
+-----------------------------+
| sdk                         |
| high-level API, artifacts,  |
| package providers, network  |
| policy helpers              |
+-------------+---------------+
              |
              v
+-----------------------------+
| runtime                     |
| workers, process lifecycle, |
| FS snapshots, host bridges  |
+-------------+---------------+
              |
              v
+-----------------------------+
| kernel                      |
| RISC-V execution, ELF,      |
| memory, FS, syscalls        |
+-------------+---------------+
              |
              v
+-----------------------------+
| RISC-V Linux userland code  |
+-----------------------------+
```

## Layer Responsibilities

The kernel owns guest-visible Linux and RISC-V behavior. Its Rust modules cover
CPU execution, ELF loading, guest memory access, filesystem state, network ring
buffers, JIT support, tracepoints, and syscall families.

The runtime owns browser and worker orchestration. It creates a kernel worker,
spawns guest processes, coordinates thread workers, manages runtime filesystem
snapshots, bridges stdio, and exposes network bridge interfaces.

The SDK owns product-facing convenience APIs. It resolves executable paths,
loads files into the guest filesystem, runs commands, applies artifact layers,
uses package providers, and exposes browser and Node-oriented integration
helpers.

The artifacts repository owns payload build recipes and release contracts. It
does not define runtime behavior. Consumers pin exact release assets and
checksums.

## Process Execution Flow

The current high-level execution flow is:

1. A host application creates a `Runtime` or `Tidemark` instance with kernel
   WebAssembly bytes.
2. The runtime initializes a kernel worker and a SharedArrayBuffer-backed page
   cache.
3. The SDK or application writes files, applies file layers, or loads an
   executable from the runtime filesystem.
4. The runtime creates a guest process and passes the executable bytes, argv,
   environment, cwd, stdio mode, and memory limits to worker orchestration code.
5. Guest execution runs through the kernel's RISC-V and syscall implementation.
6. The runtime forwards stdout, stdin, exit, filesystem, and network bridge
   events to the host application.

## Boundary Rule

Lower layers should stay generic:

- Kernel code should not know about package managers, registries, CDN paths, or
  product policy.
- Runtime code should not encode package-manager-specific behavior.
- SDK and provider code may know about artifact resolution, package profiles,
  network policy, and host integration choices.

This keeps Linux/RISC-V compatibility concerns separate from browser
orchestration and product-level provisioning.
