# State And Filesystem

Tidemark's runtime has to keep browser-side worker state and kernel-visible
guest state coherent. This page explains the current state boundaries.

## State Owners

```mermaid
flowchart TB
  SDK["SDK<br/>artifact and package decisions"]
  Runtime["Runtime API<br/>public process and filesystem methods"]
  KernelWorker["Kernel worker<br/>canonical runtime-facing FS and lifecycle state"]
  ProcessOwner["Process owner<br/>selected process state and scheduler"]
  ThreadWorker["Thread worker<br/>execution status and local continuation"]
  Kernel["Kernel wasm<br/>guest-visible semantics"]

  SDK --> Runtime
  Runtime --> KernelWorker
  Runtime --> ProcessOwner
  ProcessOwner <--> KernelWorker
  ProcessOwner <--> ThreadWorker
  ThreadWorker <--> Kernel
  KernelWorker <--> Kernel
```

The kernel defines guest-visible semantics. The runtime moves state between
workers so those semantics can survive browser worker boundaries and async host
events.

## Filesystem Layers

The runtime exposes filesystem operations such as write, bulk write, symlink,
read, readlink, mkdir, stat, readdir, snapshot loading, and file layer
application. The SDK builds on that with artifact and package provider flows.

```mermaid
sequenceDiagram
  participant App as Application
  participant SDK as SDK provider layer
  participant Resolver as ArtifactResolver
  participant Runtime as Runtime.fs
  participant KW as kernel worker
  participant Kernel as kernel memfs exports

  App->>SDK: installArtifactLayer(layerId)
  SDK->>Resolver: resolve manifest and blobs
  Resolver-->>SDK: manifest + blob URLs or Requests
  SDK->>SDK: fetch, cache, validate entries
  SDK->>Runtime: applyFileLayer(entries)
  Runtime->>KW: apply-file-layer(entries)
  KW->>Kernel: update memfs/page-cache state
  Kernel-->>KW: updated state
  KW-->>Runtime: file-layer-applied
  Runtime->>Runtime: refresh runtime FS snapshot
  SDK-->>App: ArtifactLayerManifest
```

The SDK provider interfaces let applications choose where artifact data comes
from. The runtime receives concrete file layer entries; it does not need to know
which package manager or registry produced them.

## Kernel-Worker RPC

The runtime uses request ids for kernel-worker RPCs so filesystem and lifecycle
operations can be pipelined safely. The current request set includes:

- kernel initialization,
- single and bulk file writes,
- file layer application,
- symlink creation,
- read-file and readlink,
- mkdir, stat, and readdir,
- filesystem snapshot load and snapshot export,
- process registration and lifecycle operations,
- blocked syscall resume paths,
- process and page-cache debug operations.

```mermaid
flowchart LR
  Runtime["Runtime API call"]
  Request["KernelWorkerRequest<br/>requestId + typed payload"]
  Handler["kernel-worker/message-handler.ts"]
  Action["fs, lifecycle, sync, debug operation"]
  Response["KernelWorkerOutbound<br/>requestId + typed result"]

  Runtime --> Request
  Request --> Handler
  Handler --> Action
  Action --> Response
  Response --> Runtime
```

This is a contract, not an implementation detail hidden behind a single mutable
global object.

## Snapshot Categories

The runtime message types show which state must cross worker boundaries:

| Snapshot or state | Purpose |
|---|---|
| `KernelRuntimeState` | Kernel-visible runtime state passed between kernel and workers. |
| fd entry snapshots | File descriptor to open-file-description mapping and fd flags. |
| OFD slot snapshots | Open file description state needed across process transitions. |
| pipe slot snapshots | Pipe control state and data-plane coordination. |
| socket state snapshots | Socket table and network buffer state. |
| guest memory write snapshots | Guest memory changes that must be replayed or synchronized. |
| kernel memory write snapshots | Kernel-side memory changes surfaced from execution. |
| child-exit records | Parent-visible child lifecycle records for wait-style behavior. |
| fork stack snapshots | Stack state needed across fork-style transitions. |

These categories explain why process orchestration is a central runtime
responsibility. Browser workers isolate execution, but guest processes expect a
coherent Linux-like process and file descriptor model.

## Runtime Filesystem Read Path

Runtime reads first use the most recent published runtime filesystem snapshot
when possible. If the entry is unavailable or stale, the runtime asks the kernel
worker through `read-file`.

```mermaid
flowchart TB
  Read["Runtime.readFile(path)"]
  Snapshot["runtime FS snapshot"]
  Hit{"file data present?"}
  ReturnSnapshot["return snapshot bytes"]
  RPC["kernel-worker read-file"]
  ReturnRPC["return kernel-worker bytes"]

  Read --> Snapshot
  Snapshot --> Hit
  Hit -- yes --> ReturnSnapshot
  Hit -- no --> RPC
  RPC --> ReturnRPC
```

This read path exists alongside explicit mutation paths that refresh the runtime
snapshot after writes, symlinks, directory creation, and layer application.

## Artifact Release State

Artifact releases include payload and metadata assets. The artifacts repository
validates recipe and release structure, while the SDK decides how to resolve and
apply those assets.

```mermaid
flowchart LR
  Recipe["recipe or custom build script"]
  Workflow["build artifact workflow"]
  Release["GitHub Release"]
  Payload["payload.tar.zst"]
  Manifest["manifest.json"]
  Source["source-manifest.json or sources.tar.zst"]
  License["licenses.tar.zst"]
  Checksums["sha256sums.txt"]
  BuildInfo["build-info.json"]
  SDK["SDK resolver/provider"]
  Runtime["Runtime file layer"]

  Recipe --> Workflow
  Workflow --> Release
  Release --> Payload
  Release --> Manifest
  Release --> Source
  Release --> License
  Release --> Checksums
  Release --> BuildInfo
  SDK --> Release
  SDK --> Runtime
```

The payload's upstream license remains attached to the payload. Publishing a
payload as a Tidemark artifact does not change that upstream license.
