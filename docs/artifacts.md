# Artifacts

Repository: [tidemarksh/artifacts](https://github.com/tidemarksh/artifacts)

The artifacts repository builds redistributable guest payloads and publishes
them as GitHub Release assets.

It contains recipes, build scripts, manifest validation, release contract
checks, and release automation. Payloads are not relicensed by being published
there; each payload is distributed with its own license material and source
metadata.

## Release Contract

Current artifact releases use `schemaVersion: 1` metadata and include:

```text
payload.tar.zst
manifest.json
sha256sums.txt
licenses.tar.zst
build-info.json
source-manifest.json or sources.tar.zst
```

The repository includes validation scripts for recipes and release layouts.

## Build Automation

Artifact releases are produced by the `build artifact` GitHub Actions workflow.
The workflow validates recipes, builds the requested artifact, uploads workflow
artifacts, and can publish GitHub Release assets.

Release tags use the artifact identity directly. They do not use date or build
suffixes.

## Current Recipe Families

The current recipes include examples for:

- Debian-derived runtime and toolchain payloads.
- Upstream archive payloads such as Go, Zig, Python, Java, and Rust-related
  artifacts.
- Language runtime payloads such as Node.js, Ruby, PHP, Lua, Perl, R, Elixir,
  OCaml, and Haskell.
- Toolchain and developer support payloads.
- Unresolved placeholders for payloads that do not yet have release recipes.

## Consumer Rule

Consumers should pin the exact release asset URL, size, and SHA256 they use.
The artifacts repository is a distribution boundary, not a place for generic
runtime or kernel behavior.
