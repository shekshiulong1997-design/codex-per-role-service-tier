# Compatibility and rollback boundaries

## Observed combination

| Item | Evidence-backed scope |
| --- | --- |
| Platform | Windows x86-64 / MSVC target |
| Custom backend | `0.158.0-alpha.2`, candidate hash in BUILD.md |
| Desktop package | `OpenAI.Codex_26.924.1866.0_x64__2p2nqsd0c76g0` |
| Work-agent interface | Existing V2 collaboration path used by the accepted sessions |
| Desktop integration | Current Desktop process directly parents the candidate `codex.exe app-server` |
| Request tiers | Astra root default; Luna Max priority; Sol Low and Sol High default in existing records |

Client request-tier isolation verified.
Server-side processing tier not independently verified.

This is not a claim of support for other Desktop packages, Codex tags,
Windows architectures, macOS, Linux, or an alternate backend. Historical
mock checks cover more request lifecycle branches than the small live
acceptance sample; they do not establish every real-service combination.

## Runtime components and external dependencies

The accepted runtime used `codex.exe` alongside:

- `codex-command-runner.exe` — Windows command execution helper.
- `codex-windows-sandbox-setup.exe` — Windows sandbox provisioning helper.
- `codex-code-mode-host.exe` — executor/code-mode host.

Those three helpers came from the Desktop package above. Their individual PE
version fields were unavailable; package provenance and exact hashes were
recorded privately. The source's Windows helper lookup includes sibling
executables. Mixed helper versions have not been verified.

The archive is **not standalone**: the existing Desktop application and its
managed resources, OS components, sandbox provisioning state, and configured
external runtimes remain dependencies. They were not copied wholesale.
Desktop updates may move the pinned application path or change helper/protocol
compatibility. Retaining executables does not preserve every external resource.

## Actual integration method

The existing manual launcher constructs a child process environment with
`CODEX_CLI_PATH` pointing at the candidate, then starts the pinned Desktop
`ChatGPT.exe`. It checks hashes/signatures and requires Desktop to be fully
closed and the launch to occur outside Codex in a non-administrator shell.

During packaging, the candidate was confirmed as the active app-server child
of that Desktop executable. The current process environment contained the
override, while user- and machine-level values were absent. This is active
integration for the current launch, not evidence of a persistent installed
backend replacement. Every possible shortcut, profile, or launch mechanism
has not been audited. No launch entry or environment value was changed here.

## Manual rollback for this setup

No rollback was executed. For this observed launcher-based setup:

1. Finish active work and fully quit Desktop when rollback is explicitly wanted.
2. Use the existing launcher's `-Official` option, which removes
   `CODEX_CLI_PATH` from the environment of the new Desktop process only, or
   use the installed Desktop entry from an environment without that override.
3. Confirm which app-server executable the new Desktop actually starts before
   drawing conclusions about backend selection.

This does not delete sessions, restore the whole user configuration, modify
global environment settings, or overwrite the installed runtime. Reverting
an executable does **not** guarantee backward compatibility of session/history
data. Handling the custom feature key with an official build is also not
verified here; any later targeted configuration change needs separate review.

This packaging pass performed no restart, rollback, runtime migration,
model call, full regression test, or clean rebuild.
