# Combined AgentNet PR regression check

2026-09-16. Tested code: `9c20f705`.

This branch integrates #239, #240, #241, #242, #243, #238 and the code from #244 against main `e918738`. It is a validation branch, not a replacement for those focused PRs or evidence of a deployment.

The custom-engine integration required conflict resolution in the shared runtime, generated panel, CLI, and webview. Resolutions retain Windows process dispatch, Node diagnostics, engine-specific rate limits, and custom-engine unknown context capacity. The generated panel was rebuilt from source. Webview status typing now includes the runtime context-window field.

Validation:
- Core: 70 files passed, 521 tests passed, 5 skipped. Browser panel tests were required.
- Webview: all 4 comment draft/recovery tests passed.
- Production webview build passed, with the existing chunk-size warning.
- Webview typecheck retains 10 pre-existing unused-symbol errors; no new errors relative to baseline.
- No live feed posts, wallet transactions, or deployed changes.

Recommended merge order: the focused #239–#243 and #244 first, then refresh #238 using the tested integration resolutions. This keeps each original review focused. Individual PR mergeability does not establish compatibility of the combined result.

Raw test, build and typecheck output is alongside this file. Comment UI screenshots are attached to #244. Session-concurrency issue #113 and the remaining #208 read-recovery work are not fixed by this branch.


## Follow-up: feed recovery and cloud-resume isolation

Tested integration code `1f4134e4` includes #244 at `157be722` and #245 at `dc5672f0`, in addition to the earlier PRs. Conflict resolutions preserve custom engine dispatch, feed error state and optional threads, and the Windows dependencies. Runtime changes from #245 are applied before native engine resume.

Latest result: 72 core files passed, 531 tests passed and 5 skipped. All 7 UI tests, core and localhost typechecks, and production webview build passed. Raw latest logs are attached. Individual CI for #244 and #245 passed as well.

#245 deliberately changes cloud resume into a labeled branch on each persisted resume; its copy/storage costs and acceptance limits are documented in docs/qa/cloud-resume. The integration branch does not mean maintainer acceptance, merge, deployment, or real two-device Drive verification.
