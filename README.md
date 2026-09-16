# Narrator Voice

A local, single-operator narration studio. Today it produces authorized SSGGSM Odia
diary narration (Lane B). The Lane-C Odia introduction is built but dormant, and the
future non-SSGGSM English/Hindi narration (Lane A) belongs here as another policy scope
of the same bounded context.

This repository was extracted from `voice-cloning` on 2026-09-16 under
[ADR-0013](docs/decisions/0013-narrator-voice-moves-to-its-own-repository.md), keeping
the history of every moved file. Commit hashes cited in documents written before the
split refer to `voice-cloning`; [voice-cloning-commit-map.txt](docs/voice-cloning-commit-map.txt)
translates each one to its commit here. Lecture transcription and audio enhancement stay
in `voice-cloning`.

No private audio, provider identifiers or credentials belong in this repository.

## Current SSGGSM Odia narration

The approved production baseline is an authorized Cartesia Sonic 3.6 Instant Voice
Clone for SSGGSM Odia diary narration. The accepted scope and configurable
`speed=0.90`, `volume=1.00`, `emotion=calm` defaults are recorded in
[ADR-0006](docs/decisions/0006-authorized-cartesia-ivc-for-ssggsm-odia-narration.md).
[ADR-0007](docs/decisions/0007-two-policy-isolated-narration-lanes.md) records that
this Odia lane does not replace the active future non-SSGGSM English/Hindi narration
programme and prevents their opposite identity policies from leaking across lanes.
[ADR-0008](docs/decisions/0008-lane-b-production-boundaries.md) records the accepted
one-job aggregate, split settings, separate authorization lifecycle, localhost
sole-operator UI and publishing-stage reviewer boundary. The accepted authoritative
domain background remains in the historical
[Cartesia Odia narration v3 design](docs/superpowers/specs/2026-09-02-cartesia-odia-narration-production-v3-design.md).
[ADR-0012](docs/decisions/0012-narrator-voice-v1-is-a-minimum-local-studio.md)
records that the original operational plan was overbuilt for one local operator. The
[v4 minimum local-studio design](docs/superpowers/specs/2026-09-03-cartesia-odia-narration-minimum-local-studio-v4-design.md)
and [minimum implementation plan](docs/superpowers/plans/2026-09-03-ssggsm-odia-narration-minimum-local-studio-v1.md)
govern remaining v1 work.
Tasks 3M, 4M and 5M are implemented and smoke-verified with fixture-only tests,
not hardened. The restart journey and verification limits are recorded in the
[Task 5M loopback studio plan](docs/superpowers/plans/2026-09-07-ssggsm-odia-narration-minimum-local-studio-v1-task-5m-loopback-studio.md).
On 2026-09-10 the operator authorized bounded live validation of at most ten
Cartesia calls, each no longer than one minute. This is validation authority, not
general production approval.
The current production requirement is Lane-B diary narration only. Lane-C
introduction support and its existing tests remain intact but dormant; controlled
validation must not create or generate an introduction job.
The [codebase-wide capability audit](docs/narrator-voice-shared-capability-audit-2026-09-02.md)
supports accepted [ADR-0009](docs/decisions/0009-share-narration-mechanics-not-policy.md),
which permits provider-neutral narration mechanics while keeping lane policy isolated.

### Offline Narrator Voice operations

Install the repository into its virtual environment once before using the local
operations command from a fresh checkout:

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -e ".[narrator]"
.\.venv\Scripts\python.exe -m heritage_archive.composition_root.narrator_voice_cli --help
```

Set `CARTESIA_NARRATION_ROOT` to an existing private directory outside this
repository before running commands that use the live studio. Initialization also
requires an explicit private configuration file; inspect `init --help` for its
arguments. These offline commands do not contact Cartesia or read
`CARTESIA_API_KEY`.

### Local narration website

After configuring the external studio root, the fixed local serve command is:

```powershell
.\.venv\Scripts\python.exe -m heritage_archive.composition_root.narrator_voice_cli serve
```

The website serves one operator and one episode at `http://127.0.0.1:8765`, with
one worker and no host/port overrides. It supports authoring, explicit estimates
and generation confirmation, checked playback, human review/selection and dry
intro-plus-diary mastering. Restart reloads persisted work and invalidates process
sessions and pending confirmations. Backup history is not persisted; use the
offline `backup` and `verify-backup` commands.

**Controlled live validation:** before each authorized call, run `doctor`, confirm
the current authorization and configured caps, send only one request, never replay
an unknown result automatically, and verify the immutable evidence and audio. Real
successful SSE completion currently lacks an authoritative usage source; it cannot
complete a take and keeps its budget reservation held. Treat observed failures as
the basis for focused fixes rather than adding speculative hardening. Automated
verification remains fixture-only and uses temporary roots and synthetic audio. The
live validation path uses only the configured Lane-B diary voice.

## Main components

- `src/heritage_archive/contexts/narrator_voice/`: the narration domain, application
  workflows, and local durable adapters, including the loopback website.
- `src/heritage_archive/kernel/`: canonical JSON, digests and opaque IDs.
- `src/heritage_archive/platform/`: the Cartesia transport, local file storage and
  loopback web primitives.
- `src/heritage_archive/composition_root/`: the command-line entry point and live
  studio wiring.
- `tests/heritage_archive/`: fixture-only tests, including the architecture guards.
- `docs/decisions/`: architecture decision records; `docs/superpowers/`: designs and
  implementation plans.

## Development

The full suite is fixture-only: it uses temporary roots and synthetic audio, never a
private studio root, an API key, a listening socket or a live Cartesia request.

```powershell
.\.venv\Scripts\python.exe -m unittest discover -s tests/heritage_archive -t . -v
```
