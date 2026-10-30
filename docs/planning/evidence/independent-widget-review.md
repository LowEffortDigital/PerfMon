# Independent additive-widget source review

Date: 2026-10-29. Reviewer runtime verified with the installed metadata-only runtime_provenance.py: gpt-6.1-sol, medium, openai, CLI 0.166.0-alpha.5.1. Independent reviewer agent; no delegates or product source edits.

Final reviewed source/test manifest: `widget-final-source.json`, SHA-256 `1859f45631527866775f9ac0f102c0e2b9ed5b26981b2964cf6171bf4855d8db`. All listed file hashes and the aggregate SHA-256 of the canonical compact sorted JSON file map matched before and after inspection. Baseline: `a6c637637a8934f1d63682e5cd7c712211930b85`. This report is outside the source/test manifest.

## Final outcome

No additional material correctness defect identified in the focused final review. The three P2 findings from artifact `ce32967afaf950943a90d045f0d5767e9d87cfc43b344975e0ff1d12441bc491` are resolved in the final artifact:

- Fixed stacked overflow now compares newest-aligned per-sample sums through `CardChartData.hasOverflow(style:)`; grouped/overlay plots retain individual comparison. `testStackedOverflowUsesAlignedSums` covers a sum above the ceiling while both components remain below it.
- Disconnected disk histories are pruned before append through `WidgetHistoryLifecycle.connectedDisks`; DiskMonitor removes disconnected counter baselines. Missing processes now append NaN; both Cards geometry and legacy line/area paths break at nonfinite observations instead of joining through synthetic zero. Tests cover absent-process missing samples, disconnected-history cleanup and line segmentation.
- Classic single-target content gates all legacy styles on resolved availability before invoking legacy fallback branches. Explicit metric selections use resolved chart content and matching default header values, preventing Memory/GPU cards converted to Classic from showing CPU values.

## Preliminary findings also confirmed corrected

- Grouped History Bars render both throughput directions using bounded shared geometry.
- Custom Classic network scale adapters use the configuration override before app preferences.
- Cards multi-item Grid/Sparkline consume resolved data.
- Overall CPU stacked history retains actual User/System components; individual components reject stacking.
- Process gauge data preserves four measurements with separate scales and shared layout space.

## Final gallery and drag delta review

The final review also inspected the visual two-column gallery, suppressed preview close controls, header drag overlay, nativeCardDragging opt-in and the native header hit-test assertions. No additional material correctness issue identified. Preview data remains deterministic and isolated from live telemetry; Add retains its single-activation guard. Cards opt into native AppKit movable state on window creation/update while Classic preserves its existing override behavior. The header handle excludes the close control, and the native test now checks actual hitTest identity at both regions rather than only bounds.

A minor non-blocking interaction remains: selecting an already selected device preview resets its chosen target to the first available device. This can be improved without affecting the reviewed correctness fixes.

## Evidence and limits

This review inspected the frozen source, implementation diff/new files, capability/data/scaling/render/lifecycle paths and relevant regression tests. It independently verified runtime and file digests before and after final inspection. It did not independently run builds, automated tests, hardware removal/reconnection, native interaction, screenshot comparison or performance measurements. Implementer reports 58 passing tests with one optional workload skip, successful release build and exclusion of the pre-existing live updater assertion; these are implementer execution results rather than independent test execution. Workload measurement is rerunning on this artifact and is not an outcome of this review.

The earlier native capture block is reported resolved after the user foregrounded the QA app. Implementer reports live gallery cancel/add/close and all eight styles exercised (seven network styles plus Memory Ring), with adaptive and fixed 100 Mbps observed. The user explicitly confirmed physical card dragging follows the pointer normally. These support the relevant hands-on QA claims as user/implementer observations; they are not independently reproduced by this reviewer, and synthetic dragging has not been established. Multi-display QA and exhaustive Classic before/after visual comparison remain outside the proof.

Recommendation: the reviewed source is acceptable from this focused independent correctness review. Retain the outstanding native/visual/performance acceptance limits in the handoff; do not describe the full approved acceptance matrix as completed. No installation, release or publication approval is implied. Any source/test change invalidates this artifact binding and requires a new digest/review.
