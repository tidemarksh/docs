# Runtime

Repository: [tidemarksh/runtime](https://github.com/tidemarksh/runtime)

Tidemark Runtime is a TypeScript runtime for running guest processes in browser
workers on top of the kernel.

The current package metadata identifies the package as `runtime` at version
`0.0.0`.

## Public Runtime Surface

The main exported class is `Runtime`.

Current `Runtime` capabilities include:

- `Runtime.create(options)`.
- Process spawning from executable bytes.
- Process kill and destruction.
- Runtime filesystem operations: write, bulk write, symlink, read, readlink,
  mkdir, stat, readdir, file layer application, and filesystem snapshot loading.
- Stdout callback registration and stdin writes.
- HTTP request injection through a runtime network bridge.
- Debug snapshot and execution statistics helpers.
- WebAssembly feature and SharedArrayBuffer bridge diagnostics.

## Current Source Layout

The current `runtime/src` tree includes:

```text
abi.ts
bridge.ts
bridge/
fs/
index.ts
kernel-worker.ts
kernel-worker/
messages.ts
network.ts
thread-worker.ts
thread-worker/
wasm/
worker.ts
worker/
```

Important areas:

- `index.ts`: the public `Runtime` and `Process` classes.
- `kernel-worker.ts` and `kernel-worker/`: kernel worker initialization,
  filesystem state, process control, lifecycle, and message handling.
- `thread-worker.ts` and `thread-worker/`: guest execution, blocking behavior,
  session state, signal handling, sync effects, and memory preparation.
- `worker.ts` and `worker/`: process creation, lifecycle, fork/exec handoff,
  scheduler, process I/O, runtime filesystem coordination, worker pool, and
  network socket close handling.
- `fs/`: runtime filesystem entry types, ring buffer helpers, and
  SharedArrayBuffer page cache support.
- `bridge/`: host compatibility helpers, runtime state, pipe helpers, and scope
  handling.
- `wasm/`: WebAssembly environment, feature detection, and stack helpers.

## Tests

The runtime repository contains workload tests under `tests/workloads` and
support harnesses under `tests/support`. Current workload names cover browser
direct startup, dynamic startup, script runtime, package module driver, archive
orchestrator, toolchain execution, fork/pipe behavior, network streams, and
large file operations.

## Boundary

The runtime should expose generic orchestration and host bridge mechanisms. It
should not need package-manager-specific branches to run a workload.
