# Luna Max child request-tier patch

**Unofficial patch for OpenAI Codex.**

This repository shares a source draft, not a supported release or an official
OpenAI feature. It contains source changes and documentation only, with no binaries,
installer, updater, credentials, model catalog, or conversation data.

## What the patch does

Against upstream `rust-v0.158.0-alpha.2`, commit
`10382da79a2a2d6e8ae221fa63077215389c1ad2`, the opt-in feature
`features.luna_max_subagent_fast` selects the client request tier `priority`
only for work children represented by `SubAgentSource::ThreadSpawn` whose
exact model is `gpt-6-luna` and effective reasoning effort is `max`.

The feature defaults to false. At the existing final step-settings capture,
eligible children require both FastMode and advertised model support for
`priority`. Otherwise that request is blocked before inference. The patch
does not modify model capability metadata or silently substitute a model.

Root requests and product safety-approval agents are outside this override.
Other work children retain upstream root/session tier inheritance. In
particular, Sol is **not pinned to default**: a later root tier change can
still affect Sol. This is a Luna Max selection-specific patch, not a general
role-level override or a new `service_tier` spawn parameter.

For a compatible custom build only, the opt-in setting is:

```toml
[features]
luna_max_subagent_fast = true
```

This example does not set the root model, reasoning, speed, concurrency,
permissions, or available models. Model access remains provider/account
dependent. Do not change model caches to bypass capability checks.

## Existing acceptance evidence

The following client request results were observed in existing native
execution records on the accepted Windows candidate; this packaging task
did not repeat model tests:

| Selection | Client request tier |
| --- | --- |
| Astra Root | default |
| Luna Max | priority |
| Sol Low | default |
| Sol High | default |

Client request-tier isolation verified.
Server-side processing tier not independently verified.

There is no claim of guaranteed speed, a savings percentage, or optimal
routing. A three-tier work policy is a behavioral instruction, not an
engine-level model allowlist implemented by this patch.

Read [PATCH_NOTES.md](PATCH_NOTES.md), [BUILD.md](BUILD.md), and
[COMPATIBILITY.md](COMPATIBILITY.md) before considering use. Only the
documented candidate/platform combination has acceptance evidence.

## Attribution and publication status

Upstream: [openai/codex](https://github.com/openai/codex), at the exact commit
above. The corresponding upstream [LICENSE](LICENSE) and [NOTICE](NOTICE)
are preserved verbatim. This project is not endorsed by OpenAI.

Release readiness is pending the decisions and modified-file notice review
listed in PATCH_NOTES.md. No binary release is provided.
