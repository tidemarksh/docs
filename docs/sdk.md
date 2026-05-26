# SDK

Repository: [tidemarksh/sdk](https://github.com/tidemarksh/sdk)

Tidemark SDK is a TypeScript package that provides a higher-level API on top of
the runtime.

The current package name is `@tidemarksh/sdk` at version `0.0.0`. Its current
workspace dependency points to the local `runtime` package.

## Public SDK Surface

The main exported class is `Tidemark`.

Current `Tidemark` capabilities include:

- `Tidemark.create(options)`.
- File operations: `addFile`, `writeFile`, `writeFiles`, `readFile`, `mkdir`,
  `symlink`, `stat`, and `readdir`.
- Filesystem snapshot and file layer application.
- Artifact layer installation through an `ArtifactResolver`.
- Package installation through a `PackageProvider`.
- Process operations: `spawn`, `run`, terminal attachment, and destroy.
- Debug helper forwarding from the runtime.

## Providers And Artifacts

The SDK currently exports provider and artifact types such as:

- `ArtifactResolver`.
- `ArtifactCache`.
- `ArtifactLayerManifest`.
- `PackageProvider`.
- `PackageRequest`.
- `MaterializedStaticArtifactResolver`.
- `StaticLayerPackageProvider`.
- `DebianAptPackageProvider`.
- `MemoryArtifactCache`.

The provider layer lets applications decide how to resolve, cache, verify, and
apply artifact data. Runtime and kernel code do not need to know the origin of a
file layer.

## Network Helpers

The SDK exports network-related types and helpers for policy and proxy
integration, including HTTP proxy and WebSocket tunnel helpers.

## Current Source Layout

The current `sdk/src` tree includes:

```text
index.ts
network.ts
node.ts
provisioning.ts
vite.ts
```

The tests cover the main SDK API, network helpers, Node relay behavior,
provisioning, and Debian materialization flows.
