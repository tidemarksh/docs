# Design Principles

These principles describe the public repository boundaries visible in the
current implementation.

## Separate Semantics From Orchestration

Guest-visible RISC-V and Linux userland behavior belongs in the kernel. Browser
worker lifecycle, host bridges, and scheduling live in the runtime.

This separation lets the project test compatibility behavior without tying it
to a specific product surface or package manager.

## Keep Provisioning Above Runtime Semantics

Package and artifact policy belongs in the SDK/provider layer. The runtime can
apply file layers and expose network bridge hooks, but it should not need to
know whether bytes came from a Debian package, a static artifact layer, a cache,
or a host integration.

## Make Artifacts Reproducible Enough To Pin

Artifact releases are identified by artifact identity, not by the consumer that
uses them. Each release contains payload data, manifest metadata, build
information, checksums, source metadata, and license material.

Consumers should pin exact release assets and checksums.

## Prefer Explicit Contracts

The implementation exposes explicit contracts between layers:

- Kernel exports and ABI helpers.
- Runtime worker messages and filesystem layer entries.
- SDK artifact resolvers, package providers, and network policy interfaces.
- Artifact release assets and manifest schema versions.

Explicit contracts make it easier to evolve each repository without making
lower layers depend on product-specific assumptions.
