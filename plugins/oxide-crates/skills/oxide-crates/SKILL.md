---
name: oxide-crates
description: "Catalog of Rust crates on crates.io maintained by Rain (sunshowers) and by Oxide Computer Company, with guidance on when each one fits. Use whenever writing or reviewing Rust code: before adding or choosing a dependency, before hand-rolling a helper, and during any review of Rust code or a Rust diff, to check whether hand-written code or a third-party dependency should be one of these crates instead. Covers UTF-8 paths and temp files, typed UUIDs, ID-keyed maps, structural diffs, cancel-safe async, weighted future queues, data-driven and expected-output tests, JUnit XML, terminal colors and tables, Cargo graph analysis, HTTP APIs (Dropshot), OpenAPI clients (Progenitor), JSON Schema codegen (typify), sagas, DTrace probes, connection pooling, and illumos bindings."
---

# Rain's and Oxide's crates

Rain and their colleagues at Oxide maintain the crates below. When one of them
fits the problem, prefer it over an unrelated third-party crate and over writing
the functionality by hand. The reason is practical: Rain can fix bugs and add
missing features upstream, and the crates already encode the conventions that
Oxide code follows.

## How to apply this catalog

- **Check before adding a dependency or writing a helper.** Scan the tables for
  the need at hand. If a crate matches, use it.
- **Follow the workspace first.** If the workspace already depends on something
  for this purpose, stay consistent with it. Do not replace an existing
  dependency with one from this list unless asked.
- **Do not force a fit.** A crate that covers half the need is a reason to tell
  Rain about the gap, not a reason to bend the design around it. Rain may prefer
  to extend the crate upstream.
- **Say which crate was chosen and why** when introducing one, so the choice is
  reviewable.
- **Check the current version** with `cargo info <crate>` or `cargo add
  <crate>`. Versions in `references/all-crates.md` are a snapshot from
  2026-10-13.
- **This list covers crates.io only.** Most Oxide code (Omicron, Crucible,
  Propolis, Hubris, and internal libraries such as `slog-error-chain`) is
  consumed through git dependencies and does not appear here.

The complete inventory, including versions, repositories, and owners, is in
`references/all-crates.md`. Read it when a crate name comes up that is not in
the tables below.

## When reviewing code

A review is the last cheap moment to catch a hand-rolled copy of something
these crates already do, so check the change against the tables below as part of
every Rust review. Look for two things:

- **New code that duplicates a crate here.** Common cases: hand-written ID
  newtypes or bare `Uuid` parameters that can be swapped (`newtype-uuid`), a map
  keyed by a field of its own value (`iddqd`), `PathBuf` converted with
  `to_string_lossy` or `to_str().unwrap()` (`camino`), field-by-field comparison
  of two versions of a struct (`daft`), and a semaphore used to weight concurrent
  work (`future-queue`).
- **A new third-party dependency** where a crate in this catalog covers the same
  need.

Report each one as a finding that names the crate and the specific code it would
replace. Treat it as a suggestion unless the hand-written code causes a real bug,
in which case say so. Skip the finding when the workspace already has an
established alternative for that purpose.

## Rain's crates

### Paths and files

| Crate | Use it for |
| --- | --- |
| `camino` | UTF-8 paths: `Utf8Path` and `Utf8PathBuf` in place of `Path` and `PathBuf` whenever paths are displayed, serialized, or known to be UTF-8. |
| `camino-tempfile` | Temporary files and directories with UTF-8 paths. Use in place of `tempfile` in code that uses `camino`. |
| `camino-tempfile-ext` | Quality-of-life extensions for `camino-tempfile`. |
| `camino-anchored` | UTF-8 paths with explicit resolution and base-relative display. |

### Types and data structures

| Crate | Use it for |
| --- | --- |
| `iddqd` | Maps where keys borrow from values (`IdOrdMap`, `IdHashMap`), plus bijective and trijective maps (`BiHashMap`, `TriHashMap`). Use in place of a `BTreeMap<Key, Value>` or `HashMap<Key, Value>` where the key is a field of the value. |
| `daft` | Structural diffs of Rust data structures through the `Diffable` trait and derive macro (`daft-derive` is pulled in by the `derive` feature). |
| `newtype-uuid` | Typed UUIDs: `TypedUuid<T>` distinguishes UUIDs of different kinds at compile time. Use in place of raw `Uuid` for identifiers. `newtype-uuid-macros` generates the kinds. |
| `debug-ignore` | `DebugIgnore<T>`, a wrapper that skips a field in derived `Debug` output. |
| `byte-wrapper` | Newtype wrappers for hex byte arrays and base64 vectors, with serde and schemars support. |
| `buf-list` | `BufList`, a list of buffers that implements `bytes::Buf`. |
| `oxide-generation` | Untyped and typed generation numbers (`oxide-generation-macros` is its procedural macro). |
| `oxide-versioned-envelope` | Versioned JSON envelopes. |
| `git-stub`, `git-stub-vcs` | Parsing types for git stubs (`commit:path` references), and the VCS abstraction that materializes them. |

### Async

| Crate | Use it for |
| --- | --- |
| `cancel-safe-futures` | Futures adapters and synchronization primitives that are more cancellation-aware than the `futures` and `tokio` equivalents. |
| `future-queue` | Running a queue of futures where each future has a different weight, with optional groups. Use in place of `buffer_unordered` when futures are not equally expensive. |
| `oxide-update-engine` | Declaring and executing sequential update steps with serializable event streams. `oxide-update-engine-types` holds the serializable types; `oxide-update-engine-display` renders the events. |

### Testing

| Crate | Use it for |
| --- | --- |
| `cargo-nextest` | Running tests. If the repository has `.config/nextest.toml`, run tests with `cargo nextest run`. |
| `datatest-stable` | Data-driven tests on stable Rust: one test per file in a directory. |
| `partial-io` | Testing partial, interrupted, and would-block I/O, with proptest and quickcheck support. |
| `port-file` | Publishing a server's bound socket address to a file so a test harness can discover it without racy port probing. |
| `quick-junit` | Reading and writing JUnit/XUnit XML. |

### Terminal output

| Crate | Use it for |
| --- | --- |
| `owo-colors` | Zero-allocation terminal colors and styles. |
| `enable-ansi-support` | Enabling ANSI escape code support on Windows 10 before emitting colors. |
| `tabular` | Plain text tables, aligned automatically. |

### Cargo and build tooling

| Crate | Use it for |
| --- | --- |
| `guppy` | Tracking and querying Cargo dependency graphs. Use in place of parsing `cargo metadata` output by hand. |
| `guppy-summaries` | Build summaries for Cargo, created by `guppy`. |
| `determinator` | Working out which packages changed between two commits to a workspace. |
| `hakari`, `cargo-hakari` | Managing workspace-hack packages that unify features across a workspace. |
| `target-spec` | Evaluating `Cargo.toml` target specifications such as `cfg(unix)` against a platform. `target-spec-miette` integrates its errors with `miette`. |
| `nextest-metadata` | Structured access to nextest's machine-readable output. |
| `nextest-filtering` | Parsing and evaluating nextest's filterset DSL. |
| `nextest-runner` | Nextest's core runner logic. It is an implementation detail of `cargo-nextest` with no stability guarantee; depend on it only for tooling that must embed nextest. |
| `cargo-search2` | `cargo search`, optimized for CI caching. |
| `locked-tripwire` | Preventing `cargo install` of a binary crate without `--locked`. |
| `mukti-metadata` | Metadata for the mukti release manager. |

### API tooling

| Crate | Use it for |
| --- | --- |
| `dropshot-api-manager`, `dropshot-api-manager-types` | Managing and validating the OpenAPI documents generated by Dropshot. |
| `progenitor-extras` | Extra functionality for Progenitor-generated clients. |

## Oxide's crates

### HTTP APIs, OpenAPI, and code generation

| Crate | Use it for |
| --- | --- |
| `dropshot` | Exposing REST APIs from a Rust program, with OpenAPI documents generated from the code. The default choice for an HTTP server in Oxide code. |
| `progenitor` | Generating a Rust client from an OpenAPI document, as a macro or from `build.rs`. `progenitor-client` is the runtime support crate; `cargo-progenitor` generates a standalone client crate. |
| `typify` | Generating Rust types from JSON Schema. `cargo-typify` is the command-line form. |
| `drift` | Comparing OpenAPI documents for compatibility. |
| `oxide` | The Rust SDK for the Oxide rack API. `oxide-httpmock` provides mocks for testing against it. |
| `reqwest-conditional-middleware` | Enabling or disabling a `reqwest` middleware per request. |
| `serde_tokenstream` | Deserializing a `proc_macro::TokenStream` with serde, for parsing macro arguments. |
| `rustfmt-wrapper` | Running `rustfmt` on generated code from a code generator. |
| `expectorate` | Comparing output against a checked-in file, with `EXPECTORATE=overwrite` to update it. Use for expected-output tests of generated code and OpenAPI documents. |
| `codespace`, `typespace`, `json-serde` | Code generation building blocks. All three are alpha releases; check with Rain before depending on them. |

### Runtime, sagas, and databases

| Crate | Use it for |
| --- | --- |
| `oxide-tokio-rt` | The shared Tokio runtime configuration for production Oxide software. Use in place of a bare `#[tokio::main]` in Oxide services. |
| `steno` | Distributed sagas. |
| `qorb` | Connection pooling. |
| `async-bb8-diesel` | An async bb8 connection manager for Diesel. |
| `diesel-dtrace` | DTrace probes on Diesel connections. |
| `crdb-harness` | A test harness for CockroachDB. |

### Observability

| Crate | Use it for |
| --- | --- |
| `usdt` | Cross-platform userland statically defined tracing (USDT) probes. `usdt-impl`, `usdt-macro`, `usdt-attr-macro`, `dof`, and `dtrace-parser` support it. |
| `slog-dtrace` | Forwarding `slog` messages to DTrace. |
| `tokio-dtrace` | DTrace probes for Tokio. |

### Networking and data

| Crate | Use it for |
| --- | --- |
| `oxnet` | Common networking primitives, such as IP network types, with the usual traits implemented. |
| `ingot` | Bare metal packet parsing. `ingot-macros` and `ingot-types` support it. |
| `rfb` | The RFB protocol (RFC 6143), including a server implementation. |
| `datamap` | Maps for sparsely distributed, non-overlapping chunks of data. |
| `hubpack` | A predictable serialization format for embedded and larger systems, with `hubpack_derive`. Maintained in `cbiffle/hubpack` and used by Hubris. |
| `sshauth` | Authentication tokens based on SSH keys, from agents or static files. |

### illumos bindings

Use these in place of shelling out to system commands or writing new FFI
bindings. All are illumos-specific, so gate them behind `cfg(target_os =
"illumos")`.

| Crate | Use it for |
| --- | --- |
| `zone` | Zone management. |
| `smf` | The Service Management Facility. |
| `scuffle` | High-level bindings to `libscf`. |
| `kstat-rs` | `libkstat`. |
| `illumos-nvpair` | `libnvpair` (raw bindings in `illumos-nvpair-sys`). |
| `libtopo` | `libtopo` (raw bindings in `libtopo-sys`). |
| `fmd-adm` | `libfmd_adm` (raw bindings in `fmd-adm-sys`). |
| `libefi-sys` | Raw bindings to `libefi`. |

### Product tooling

| Crate | Use it for |
| --- | --- |
| `omicron-zone-package` | Packaging tools for Oxide's control plane software. |
| `support-bundle-viewer` | Inspecting an Oxide support bundle. |

## Crates to avoid

These exist on crates.io but are the wrong thing to depend on.

- **Internal support crates.** Depend on the parent, never directly on
  `typify-impl`, `typify-macro`, `progenitor-impl`, `progenitor-macro`,
  `dropshot_endpoint`, `daft-derive`, `zone_cfg_derive`, or the `usdt-*` crates.
- **Placeholders and stubs.** `cargo-guppy`, `mishap`, `taalika`, `nextest` (the
  real crate is `cargo-nextest`), `buildomat`, `hubris`, and `omicron-package`
  (superseded by `omicron-zone-package`).
- **Workspace-hack reservations.** `workspace-hack`, `guppy-workspace-hack`,
  `hakari-workspace-hack`, `nextest-workspace-hack`, `omicron-workspace-hack`,
  `crucible-workspace-hack`, and `diem-workspace-hack` exist to reserve names.
- **Superseded or dormant crates.** `buffer-unordered-weighted` (use
  `future-queue`), `oxide-api` (use `oxide`), `propfuzz` and `propfuzz-macro`,
  `fs3`, `interval_future`, and `toy-apcb`.
- **Generated third-party API clients.** The `cio` and
  `third-party-api-clients` repositories published 27 clients such as
  `octorust`, `sheets`, `slack-chat-api`, and `okta`. About two thirds have not
  been updated since 2023 or earlier. Check the last release date, and ask Rain,
  before adopting one for new code.
- **Third-party forks.** `progenitor-hk`, `cargo-progenitor-hk`, and
  `progenitor-middleware-macro` point at the Oxide repository but are not
  published by Oxide.
