# Cloud resume writer isolation — issue #113

## Behavior

A persisted resume through a configured cloud mirror copies its observed canonical history to a new UUID before writing any new metadata or messages. Each concurrent resume owns different page keys. The original remains unchanged, and session listing shows both copies with a `cloud resume <id>` suffix. Local-only and ephemeral resumes do not fork.

This deliberately forks every persisted cloud resume, including same-device resumes and model-change respawns. The adapter has no atomic compare-and-swap or fenced lease, so checking a marker or reading a page immediately before append cannot safely distinguish concurrent writers. The tradeoff is additional session entries, full-history copying latency, storage and upload traffic. Maintainer acceptance of this behavior is required; it is not a transparent conflict detector.

A missing source page aborts before writing destination pages. Failure during destination writes can still leave a partial *new* branch; source pages are never overwritten. This does not reconcile branches already overwritten by older clients, make mixed old-client writers safe, or fix other mutable mirrored data such as shared memory.

## Evidence

The test drives createRuntime.startSession, SessionStore encryption/decryption, native-ID selection, mirror debounce and backfill. Only the engines/native history injection and cloud transport are replaced with local fixtures. No credentials, real Drive files, model calls or on-chain writes are used.

Five regressions cover:
- simultaneous resumes of a full page, rollover and independent new messages (one Korean);
- a fresh cloud reader seeing both complete branches and all original bytes unchanged;
- unchanged local-only resume behavior;
- missing-page rejection before any destination page is created;
- ephemeral no-write behavior and offline branch recovery through backfill.

The same tests against unchanged runtime/store code fail three cases and pass the two compatibility cases. The fixed full core suite passes 480 tests (66 files), with browser panel tests required. Core typecheck passes. Full-suite concurrency was limited to two workers after the initial run hit the existing fixed-50ms session-settings test race and the new debounce test's initial five-second timeout. The debounce test now has a 15-second limit; its assertions are unchanged.

Raw before/after results are attached. This is local runtime/storage proof, not a real two-device Drive or engine acceptance run. No UI layout is changed by this patch; the existing session list receives separate IDs and labeled titles.
