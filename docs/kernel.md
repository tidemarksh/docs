# Kernel

Repository: [tidemarksh/kernel](https://github.com/tidemarksh/kernel)

Tidemark Kernel is a Rust/WebAssembly RISC-V Linux userland kernel. The crate is
configured to use `no_std` for the `wasm32-unknown-unknown` target and exposes
modules for guest execution and Linux userland behavior.

## Current Workspace

The kernel repository is a Cargo workspace with:

- `kernel/`: the kernel crate.
- `tests/`: the Rust test crate and test runner support.
- `vendor/riscv-tests`: vendored RISC-V tests.
- `vendor/ltp`: vendored Linux Test Project sources.

## Current Kernel Modules

The current `kernel/kernel/src` tree includes:

```text
abi.rs
alloc.rs
cpu/
elf.rs
exports/
fs/
guest_mem.rs
harness/
jit/
net/
syscall/
tracepoints/
types.rs
```

Important module groups:

- `cpu/`: instruction decode, dispatch, integer, floating point, atomics,
  system instructions, block execution, block cache, trace support, and faults.
- `elf.rs`: ELF loading support.
- `guest_mem.rs`: guest memory access helpers.
- `fs/`: in-memory filesystem, fd table, and ring buffer support.
- `syscall/`: syscall dispatch and syscall-family modules for filesystem,
  process, signal, socket, pipe, poll, epoll, memory, identity, resource, PTY,
  thread, and time behavior.
- `jit/`: JIT cache and WebAssembly generation modules.
- `exports/`: WebAssembly-facing exports for kernel, thread, and memfs entry
  points.
- `harness/`: test and debug-oriented exports enabled for test or harness
  builds.

## What The Kernel Does Not Own

The kernel does not own browser worker creation, package resolution, CDN
configuration, registry policy, or product UI behavior. Those concerns live in
runtime, SDK, provider, or application code.
