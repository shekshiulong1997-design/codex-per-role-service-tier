# Patch notes

Scope: Luna Max work-child client request-tier selection and directly related
schema, feature registration, and tests. Baseline:
`10382da79a2a2d6e8ae221fa63077215389c1ad2` (`rust-v0.158.0-alpha.2`).

## Exact changed files

| File | Change |
| --- | --- |
| `codex-rs/features/src/lib.rs` | Register disabled-by-default `LunaMaxSubagentFast`. |
| `codex-rs/core/config.schema.json` | Add the boolean feature to the two existing feature-schema locations. |
| `codex-rs/core/src/session/mod.rs` | Select priority for exact Luna/Max work children at step capture; fail before inference if the required capability or FastMode is absent. |
| `codex-rs/core/tests/suite/subagent_service_tier.rs` | Extend existing mock request assertions for routing, root isolation, continued work, compaction, reload, and capability rejection. |

`patches/service-tier.patch` was exported from the exact upstream commit to
the current working source, not merely from local HEAD to the working tree.
Local HEAD equals that upstream commit; the feature changes are uncommitted.
There are no required new runtime source files. Unrelated newline-only
working-tree differences and a local development report are excluded.

Patch SHA-256:
`d0acb99c97d1729bbe45973d857a78f5d1484a70ddab3082e2b935c7f76a5057`.

## Existing verification and limits

Retained build records report 42 feature tests and 12 focused service-tier
tests passed with no retries. The 12 include four existing regression cases
and eight new parameterized cases. They cover explicit/default Luna Max,
Sol Low/High, non-Max Luna, feature disabled, missing priority capability,
and FastMode disabled. Model fixtures are synthetic local mock data, not
changes to an account model catalog.

This packaging task only verified archive hashes, patch scope, draft
contents, and one `git apply --check` against an independent temporary copy
of the four exact upstream files. The patch was not applied there. Apply
success is not a rebuild, runtime test, or portability guarantee.

Client request-tier isolation verified.
Server-side processing tier not independently verified.

## Before a supported release

- Confirm attribution/contributor identity and the intended publication scope.
- Review the upstream Apache-2.0 Section 4(b) modified-file notice requirement
  for all four files, including the generated JSON schema. The exact source
  changes currently lack a consistent, prominent modification notice in each
  changed file. File-level notices were not injected into the live source or
  this exact-diff patch. This remains a publication TODO; the notes here do
  not claim to discharge that requirement.
- Decide whether to perform a clean rebuild and any additional compatibility
  checks. Neither has been performed for this sharing draft.
- Decide the supported version/platform statement and any future distribution
  of binaries. This draft intentionally distributes no executables or bundled
  Desktop helpers and makes no claim about their redistribution terms.
- Keep the separate private archive and its machine-specific evidence outside
  this repository; only the seven reviewed source-draft files belong here.
