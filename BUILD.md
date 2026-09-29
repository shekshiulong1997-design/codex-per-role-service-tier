# Recorded Windows build

This documents the existing candidate build. No compilation, tool installation,
dependency download, or model test was performed while preparing this draft.
**A reproducible build from a clean environment has not been verified.**

## Source and output identity

- Upstream tag: `rust-v0.158.0-alpha.2`.
- Upstream commit and local source HEAD:
  `10382da79a2a2d6e8ae221fa63077215389c1ad2`.
- Four uncommitted feature files are represented by `patches/service-tier.patch`.
- Candidate CLI version in existing execution metadata: `0.158.0-alpha.2`.
- Candidate: Windows AMD64 PE, unsigned, 322,334,720 bytes.
- Candidate SHA-256:
  `a344288cd0787f01a1d77a30be91e66bce66ddb925cb63d5e19e7b96b825011c`.

The candidate and the four final source-file hashes match retained build
records. Two generated/test files had their original CRLF restored after
compilation; their LF-normalized hashes also match the recorded build inputs.
There is no complete independently attested snapshot of all compiler inputs.
**Binary/source correspondence is not fully confirmed.** A matching version
string, filename, or these limited hashes is not proof of a reproducible build.

## Recorded dependencies

| Component | Recorded version or setting |
| --- | --- |
| Rust | `1.95.0-x86_64-pc-windows-msvc`; rustfmt, clippy, rust-src |
| Compiler environment | VS 2022 Build Tools; MSVC `14.44.35207` |
| Windows SDK | `10.0.26100.0`; CMake installed with the build tools |
| just | `1.58.0` |
| cargo-nextest | `0.9.146` |
| DotSlash | `0.5.9` |
| Python / uv | Existing local installations; exact versions not retained in the selected build record |
| V8 artifacts | Upstream `rusty-v8-v150.4.0`, Windows MSVC pointer-compression/sandbox release library and Rust bindings |

V8 artifact SHA-256 values in the retained download record:

- `rusty_v8_ptrcomp_sandbox_release_x86_64-pc-windows-msvc.lib.gz`:
  `732ec5da4243aa166799780c8519a5eea6f32f6e47657a323342794dc3c239d6`.
- `src_binding_ptrcomp_sandbox_release_x86_64-pc-windows-msvc.rs`:
  `dabf78ba1faac127660db9862b1d0354175c71b8db2d4fcb5bacbd9c93576b16`.

## Recorded sequence

1. Use the exact upstream revision in a short Windows workspace and apply the
   supplied patch. The original build used a temporary short drive mapping
   after path-length failures; that mapping was subsequently removed.
2. Establish the MSVC x64 build environment and isolated Rust/tool caches.
   The retained wrapper set `CARGO_BUILD_JOBS=4`,
   `LIBSQLITE3_FLAGS=SQLITE_DISABLE_INTRINSIC`, `PYTHONUTF8=1`,
   `UV_PYTHON_DOWNLOADS=never`, separate `RUSTUP_HOME`, `CARGO_HOME`,
   `CARGO_TARGET_DIR`, `UV_CACHE_DIR`, and `DOTSLASH_CACHE` locations, plus
   explicit existing Python and verified V8 library/binding paths. It selected
   the toolchain's `rust-lld.exe` as the MSVC target linker when available.
   Machine-specific paths and that wrapper are intentionally not published.
3. In `codex-rs`, generate the schema using `just write-config-schema` and
   review the lockfile state before locked commands. During the original
   preparation, Cargo changed 156 local workspace package version labels
   from `0.0.0` to `0.158.0-alpha.2`; the recorded comparison found no
   third-party dependency changes. That generated lockfile was used for the
   build, then the source lockfile was restored. It is a transient build input,
   not an extra unrelated lockfile change hidden in this patch. Running
   `--locked` immediately on a fresh tag checkout may therefore fail; the
   clean-environment preparation sequence remains to be verified.
4. The recorded focused checks were:

   ```text
   just test -p codex-features --locked --retries 0
   just test -p codex-core --test all --locked --retries 0 -E 'test(subagent_service_tier)'
   ```

5. The recorded release command, from `codex-rs`, was:

   ```text
   cargo build --locked --release --target x86_64-pc-windows-msvc --bin codex
   ```

It completed with exit 0 in 60m 52s. Output was
`<CARGO_TARGET_DIR>/x86_64-pc-windows-msvc/release/codex.exe`.
The upstream release profile uses thin LTO, line-table debug information,
no stripping, and four codegen units. The target config adds an 8 MiB stack
and static CRT linkage. The PDB is not part of this sharing draft or the
minimal runtime archive.

Whole-repository formatting failed during the historical preparation;
focused formatting passed. The full workspace test suite was not run.
Three warnings remained in unchanged upstream files. These facts are not
rewritten as clean-build or complete-test passes.

The three Desktop helper executables were copied from the installed Desktop
package after building `codex`; they were not built by the command above.
See COMPATIBILITY.md for the runtime boundary.
