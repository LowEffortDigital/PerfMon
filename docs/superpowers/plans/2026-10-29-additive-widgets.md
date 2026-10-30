# Additive Widgets and Adaptive Network Graphs Implementation Plan

> **For the Sol implementer:** Use `superpowers:executing-plans` to implement this plan task by task. Work natively as GPT-6.1 Sol / medium after a metadata-only runtime probe. Do not spawn agents unless separately authorized by the user or applicable repository instructions. Steps use checkboxes for tracking.

**Goal:** Add attractive optional desktop cards and a gallery while making every supported widget graph render the intended metric correctly, with opt-in adaptive network scaling.

**Architecture:** Extend the existing custom-widget model and NSWindow lifecycle. Resolve immutable, unit-aware chart data once, render it through a shared graph component, and select either the preserved Classic shell or the new Cards shell. Keep network domain selection pure and separate from warning colours.

**Tech stack:** Swift tools 5.10, SwiftUI, AppKit, Canvas, Combine, XCTest, macOS 14 minimum; no new external packages.

**Spec:** [Additive widget design](../specs/2026-10-29-additive-widgets-design.md). Read it together with [checkpoint](../../planning/CHECKPOINT.md) and [decisions](../../planning/DECISIONS.md).

## Global constraints

- Existing widgets, IDs, enabled state, scope, header tokens, dimensions, layout limits, user colours, window positions and menu-bar settings remain intact.
- Missing presentation setting means Classic. Missing network-scale setting means inherit app preference; missing app preference means Legacy.
- Keep existing serialized chart raw strings: `Grid Chart`, `Sparkline`, `Bar Gauges`, `Numeric Only`.
- Gallery opening and cancellation are read-only. `Add Widget` creates exactly one record with a fresh UUID.
- Adaptive scaling changes graph height, not warning thresholds. Preserve `Pref_NetAmberMBps` and `Pref_NetRedMBps` and their current semantics.
- No unsupported battery/temperature/daily-traffic cards; no fabricated history, no different-target fallback, no fake percent utilization for network rates.
- macOS 14 remains supported. Do not add a horizontal history browser or a new sampling timer.
- No application install, release tag, release/distribution workflow, website sync, remote push or merge is authorized by this plan.
- No implementation/test/live/review outcome may be marked complete without evidence bound to the actual artifact revision or digest.

## Review focus

1. An old multi-item saved widget must retain its identity/layout yet honour Bar Gauges/Numeric Only. Cover in Tasks 1, 4 and 8.
2. Two windows tracking the same target must not double sample, and disappearing targets must not show somebody else's traffic. Cover in Tasks 2 and 8.
3. Upload-only traffic, a spike leaving the plotted range, and stacked sums must resolve to the correct shared scale. Cover in Tasks 3 and 5.
4. Startup, one point, invalid doubles, out-of-range process CPU, tiny views and long names must not produce blank/misleading or clipped content. Cover in Tasks 2, 4 and 8.
5. Opening a preview, duplicating a card, resizing it, and relaunching must not overwrite another widget's preferences or position. Cover in Tasks 1, 6, 7 and 8.

## Source map and implementation seams

Existing integration files:

| File | Work |
| --- | --- |
| `Models/CustomWidgetModels.swift` | Append graph cases; backward-compatible optional/defaulted configuration. |
| `Models/WidgetPreferences.swift` | Persist scale preference, copy all config fields on duplication, make new preference tests isolated. |
| `Models/SystemMetrics.swift`, `Engine/SystemMonitorEngine.swift` | Sampling metadata/availability, CPU paired histories, unique custom history collection. |
| `Engine/NetworkMonitor.swift`, `Engine/GPUMonitor.swift` | Only narrow availability/reconnect handling described in Task 2; no aggregate-counter redesign. |
| `Views/Widgets/CustomModularWidgetView.swift` | Keep observed app boundary, route to Classic/Cards and common data/graph renderer. |
| `Views/Components/TaskManagerGridChart.swift`, `MetricSparkline.swift` | Preserve appearance; shared geometry sanitization/gap behavior where needed. |
| `Views/Settings/CustomWidgetSettingsView.swift` | Add gallery entry, presentation/metric/scale selection and shared layout preview. |
| `App/WindowManager.swift` | Use the same size resolver for preview/show/update; preserve identity and placement. |
| Existing six network graph entry points named in Task 5 | Mode-aware scale adapters, Legacy default. |

All paths above are relative to `Sources/MacTaskManager/`. New files are named in the owning tasks. Keep renderer internals split only where responsibility warrants it; do not turn the already large settings view into another omnibus implementation.

## Task 0 — Establish the protected baseline

**Files:** read existing sources, `.github/workflows/ci.yml`, `Scripts/build_app.sh`; update `docs/planning/CHECKPOINT.md` with actual implementation baseline.

- [ ] Run `python3 /Users/james/.codex/scripts/runtime_provenance.py`; require verified GPT-6.1 Sol / medium or user-authorized alternative. Record metadata only.
- [ ] In `/Users/james/PerfMon`, run `git status --short`, `git branch --show-current`, `git rev-parse HEAD`. Planning baseline was clean `main` at `a6c637637a8934f1d63682e5cd7c712211930b85`; reread changed code if HEAD differs. Preserve planning files and concurrent edits.
- [ ] Use an isolated implementation checkout/branch `codex/additive-widget-gallery` if the existing workspace is shared; follow worktree guidance. Do not create a second branch if the user already supplied an appropriate one.
- [ ] Run `swift --version` and `swift build`. Record baseline failures without attributing them to this feature. No need to package or replace `dist/PerfMon.app` during this step.
- [ ] Capture baseline Classic widgets with sanitized fixture configurations: global/single disk/single network/process and all four multi scopes, all four existing styles, light/dark. Establish a deterministic render harness before comparing images. Record current known dispatch failures separately; do not bless them as the intended result.
- [ ] Inspect test isolation before full `swift test`: existing tests write `UserDefaults.standard`, use singleton engines, create windows, scan hardware and include a live update check. Verify the test bundle's preference domain cannot touch the user's app domain, or run the full suite only in a clean CI/test-user environment. Do not reset user settings as test setup.

**Acceptance:** verified source/runtime baseline, build result and Classic visual baseline; no app configuration changed by the investigation. Baseline failures remain explicit in checkpoint.

## Task 1 — Add compatible configuration and capability rules

**Files:** create `Models/WidgetPresentation.swift`, `Models/WidgetChartCapability.swift`; modify `Models/CustomWidgetModels.swift`, `Models/WidgetPreferences.swift`; create `Tests/MacTaskManagerTests/WidgetConfigurationTests.swift`.

**Interfaces:**

- `WidgetPresentationStyle: String, Codable, CaseIterable, Sendable` with `.classic`, `.cards`.
- `WidgetMetricSelection: String, Codable, CaseIterable, Sendable` with `.automatic`, `.cpuUsage`, `.memoryUsage`, `.diskCapacity`, `.diskThroughput`, `.networkThroughput`, `.gpuUsage`, `.processCPU`.
- Add config properties `presentation: WidgetPresentationStyle = .classic`, `metric: WidgetMetricSelection = .automatic`, `networkScale: NetworkGraphScaleSettings? = nil`. Network types are defined in Task 3; create their type declarations now so this task compiles.
- Append chart cases `.historyBars = "History Bars"`, `.stackedHistoryBars = "Stacked History Bars"`, `.ringGauge = "Ring Gauge"`, `.dualArea = "Dual Area"`.
- `WidgetChartCapability.support(style: WidgetChartStyle, dataKind: WidgetChartDataKind, hasHistory: Bool) -> WidgetChartSupport`, returning `.supported` or `.unsupported(reason: String)`. Define `WidgetChartDataKind: Equatable, Sendable` with boundedPercentage, byteCapacity, cpuComposition, throughputPair, processCPU, categoricalStatus. `hasHistory` prevents capacity-only disks from offering invented histories.
- `CustomWidgetConfig.duplicated(id: UUID, title: String) -> CustomWidgetConfig` preserves all fields except supplied ID/title and enabled=true, matching current duplicate behavior.

- [ ] Add tests `testLegacyDecodePreservesEveryField`, `testNewFieldsRoundTrip`, `testEightChartCasesPreserveLegacyRawValues`, `testDuplicateCopiesLayoutAndScale`, `testCapabilityMatrix`. Use fixed sanitized JSON; legacy JSON lacks all three new fields and must decode to Classic/Automatic/nil.
- [ ] Run `swift test --filter WidgetConfigurationTests`; capture expected initial failing assertions or missing-symbol compile failures, then implement declarations/decoding/duplication and rerun to green.
- [ ] Keep unknown/new-field parsing from triggering destructive persistence. A configuration load failure must not overwrite the original stored bytes with an empty collection. Missing fields get defaults; invalid present values should produce a diagnosable load/validation state rather than an unannounced migration.
- [ ] Use temporary `UserDefaults(suiteName:)` storage for added persistence tests, cleaned with `defer`. If introducing `init(defaults:)` on WidgetPreferences, retain its production `.shared` using `.standard`; avoid theme/window side effects in fixture tests.
- [ ] Confirm the initial four-style defaults and manually-created Classic widget are unchanged. Update decisions/checkpoint at task completion.

## Task 2 — Resolve truthful data and collect one sample per tick

**Files:** create `Models/WidgetChartData.swift`, `Engine/WidgetChartDataResolver.swift`, `Engine/CustomHistoryCollector.swift`; modify `Engine/SystemMonitorEngine.swift`, `Models/SystemMetrics.swift`, narrowly `Engine/NetworkMonitor.swift` and `Engine/GPUMonitor.swift`; create `WidgetChartDataTests.swift` and `CustomHistoryCollectorTests.swift` in the test target.

**Interfaces:**

- `WidgetChartSeries: Sendable` carries `id: String`, `label: String`, `values: [Double?]` oldest-to-newest.
- `WidgetChartData: Sendable` carries `kind: WidgetChartDataKind`, `unit: WidgetChartUnit`, `series: [WidgetChartSeries]`, `currentValues: [Double?]`, `boundedTotal: Double?`, `availability: WidgetDataAvailability`, primary/secondary display text, target label, `readouts: [WidgetMetricReadout]` and `gaugeRows: [WidgetGaugeDatum]`. Units: percent, bytes, bytesPerSecond, count, status. Availability: available, warmingUp, unavailable(reason: String).
- `WidgetMetricReadout: Sendable` carries id, label, formatted value and its own unit. `WidgetGaugeDatum: Sendable` adds optional numeric value and finite positive ceiling. Preserve Classic process CPU/RAM numeric pairs and CPU/RAM/disk-I/O/threads gauge rows, plus the global CPU/RAM gauge pair. These can have different units; never feed mixed units into one plotted axis.
- `WidgetTelemetrySnapshot: Sendable` contains value copies of existing cpu/memory/disk/network/gpu samples, processes and history dictionaries plus validity/segment metadata. Construct it at the observed view boundary; pure resolver/tests never initialize SystemMonitorEngine.shared.
- `WidgetChartDataResolver.resolve(config: CustomWidgetConfig, item: MultiObjectItem?, snapshot: WidgetTelemetrySnapshot) -> WidgetChartData`. `item == nil` means a single scope; nonnil means one multi-item tile. Automatic reproduces existing metric intent, with explicit plotted-series labels.
- `CustomHistoryCollector.samples(configs: [CustomWidgetConfig], snapshot: WidgetTelemetrySnapshot) -> [String: Double]`: one value per unique history key for that engine tick. Preserve process-vs-hierarchy identity; reject conflicting key meanings rather than last-writer-wins data.

- [ ] Test `testDuplicateTargetsAppendOncePerTick`: two configs plus a multi-object item sharing a target produce one history append, not three. Use monotonically increasing sample values and assert the last three samples equal exactly the last three ticks.
- [ ] Test missing disk/interface with busy global history returns unavailable and no global samples; process not running remains explicitly not running; RAM used bytes never receives percentage history; categorical status has named states with no percent unit.
- [ ] Test empty/one-sample/startup validity, unequal paired lengths (left-pad missing), true zero vs unavailable, target disappearance/reappearance, and process CPU 250% on an 8-logical-core fixture. Preserve the number 250 and its visible range.
- [ ] Implement unique collection outside the per-config append loop. Add CPU User/System histories from existing CPUSample fields for stacked CPU bars. Keep total CPU history untouched.
- [ ] Retain 60-sample capacity and track how many samples were actually observed; mask initial padding as missing in new render data. Align disconnected histories with explicit missing segments or reset their validity on reappearance. Do not stretch a sample window because a second widget uses it.
- [ ] For interface reconnect, remove obsolete previous-interface baselines when an interface disappears, so its first returning rate is zero/warming-up rather than averaged across the absence. Add `NetworkInterfaceItem.hasRateSample`, default false, true only after a valid continuous counter delta; the pure resolver exposes warmingUp until then. Test with an extracted pure counter-delta helper. Do not change the global aggregate-counter algorithm without separate reproduced evidence.
- [ ] Add `GPUSample.hasUtilizationSample` default false, set true only when a real utilization property is read. New Cards use this to distinguish unsupported hardware from real 0%; existing numeric fields stay available.
- [ ] Run `swift test --filter 'WidgetChartDataTests|CustomHistoryCollectorTests'` and inspect every result. Confirm no fixture test starts live collectors.

## Task 3 — Implement pure adaptive scale resolution

**Files:** create `Models/NetworkGraphScale.swift`; create `NetworkGraphScaleTests.swift`.

**Interfaces:**

- `NetworkGraphScaleMode: String, Codable, Sendable` = legacy, adaptiveVisible, fixed.
- `NetworkGraphScaleSettings: Codable, Hashable, Sendable` = mode plus `fixedCeilingBytesPerSecond: Double`, default 12_500_000 (100 Mbps) for the fixed editor value.
- `NetworkGraphScaleResolver.resolve(settings: NetworkGraphScaleSettings, series: [[Double?]], visibleRange: Range<Int>, stacked: Bool, legacyCeiling: Double) -> NetworkGraphDomain`.
- `NetworkGraphDomain: Equatable, Sendable` = `upperBoundBytesPerSecond: Double`, `visiblePeakBytesPerSecond: Double`, `hasOverflow: Bool`. Lower bound is always zero. Invalid user input is a validation error before committing settings; resolver still returns a finite safe fallback for corrupt persisted data.

- [ ] Write exact fixtures: 1_000_000 down and 250_000 up B/s → ceiling 1_250_000 B/s; stacked 1_000_000 + 500_000 → 2_500_000 B/s; available zeros/empty → 125 B/s; fixed 1_250_000 with sample 2_500_000 → same ceiling and overflow=true.
- [ ] Add `testPeakExpiresWithVisibleRange`, `testUploadOnlyControlsDomain`, `testHiddenSeriesDoesNotControlDomain`, `testAggregateSumsAlignedSamplesNotPeaks`, `testFixedUnitConversion`, `testInvalidInputsNeverProduceNonfiniteDomain`, and exact 1/2/5 boundary cases. A negative/out-of-bounds/empty range must be safely intersected with available indexes.
- [ ] Implement the spec's 10% headroom and 1/2/5 decimal-bps upward rounding. Preserve missing values; clamp negative finite values; guard overflow with bounded operations. Legacy returns its supplied view-specific ceiling without applying new headroom.
- [ ] Run `swift test --filter NetworkGraphScaleTests`, verify expected failures before implementation and a clean result afterward. Add tests asserting colour-threshold values are independent of scale settings.

## Task 4 — Make every supported chart type render explicitly

**Files:** create `Views/Components/WidgetCharts/WidgetChartView.swift`, `WidgetChartGeometry.swift`; modify `CustomModularWidgetView.swift`, `TaskManagerGridChart.swift`, `MetricSparkline.swift` only at required seams; create `WidgetChartGeometryTests.swift`, `WidgetChartRenderingTests.swift` and deterministic test fixtures.

**Interfaces:**

- `WidgetChartView(data: WidgetChartData, style: WidgetChartStyle, domain: WidgetChartDomain, palette: WidgetChartPalette)` is the stateless graph content. Define `WidgetChartDomain` as lower/upper finite bounds plus optional overflow; `WidgetChartPalette` supplies semantic colours, not preference access.
- `WidgetChartGeometry.make(series: [WidgetChartSeries], domain: WidgetChartDomain, size: CGSize) -> WidgetChartGeometry` contains testable normalized segments/points and finite bounded positions. Rendering must consume this geometry rather than maintaining a divergent second formula.
- A single exhaustive switch dispatches all eight styles in both single and multi-item paths. Unsupported combinations return the explicit capability reason.

- [ ] Test the existing regression first: a multi-object Numeric Only config produces numeric content with zero plot paths; Bar Gauges produces gauge rows, not a grid. Parameterize all four legacy styles across global/specificDisk/specificNetwork/process/multiDisk/multiNetwork/multiProcess/multiObject.
- [ ] Implement line/grid and sparkline with gaps and one-point marks; vertical History Bars; genuine stacked components; bounded rings; and two labelled area series sharing a domain. Numeric Only uses current values and availability only. Zero-filled gauges have zero fill, not the current forced 3 pt minimum.
- [ ] Keep Classic Grid/Sparkline line widths, grid, fills and dimensions unless a geometry safety fix requires a documented difference. Label read-only/download-only Classic plots truthfully. New paired Cards plot both directions. Never silently replace a saved graph style.
- [ ] Render ring 0%, 53.4%, 100%; zero denominator gives unavailable, not a full or empty trustworthy ring. Throughput rings are unsupported. Stacked totals are sums at each aligned sample and never exceed plot bounds unnoticed.
- [ ] Test NaN, ±infinity, negatives, one point, all zero, zero/tiny size, tall/narrow size, uneven series and process CPU >100. Missing samples break paths. Verify geometry is finite and inside the plot; verified current values remain unclipped text.
- [ ] Render native SwiftUI snapshots with ImageRenderer from pure fixture views in light/dark and 1×/2×. Assert dimensions and visible chart ink for nonzero fixtures, but also inspect contact sheets: image existence/nonempty bytes alone is not visual correctness.
- [ ] Run `swift test --filter 'WidgetChartGeometryTests|WidgetChartRenderingTests'`. Preserve generated evidence outside checked-in raw user data. Bind reviewed snapshots to source digest.

## Task 5 — Wire opt-in scale settings through network views

**Files:** modify `WidgetPreferences.swift`, `SettingsTabView.swift`, `CustomWidgetSettingsView.swift`, `Views/Widgets/NetworkWidgetView.swift`, `AllInOneHUDWidgetView.swift`, `CustomModularWidgetView.swift`, `Views/Performance/NetworkDetailView.swift`, `PerformanceTabView.swift`, `Views/Network/NetworkSectionView.swift`; create `NetworkGraphIntegrationTests.swift`.

**Interfaces:** app `networkGraphScaleSettings` persisted under a new namespaced key, default Legacy. Effective custom setting is `config.networkScale ?? prefs.networkGraphScaleSettings`. Existing warning threshold setters are untouched.

- [ ] Add settings `Graph scaling: Legacy / Adaptive visible / Fixed`, optional per-widget `Use app setting`, and conditional Fixed ceiling in Mbps. Copy: `Adaptive scaling changes chart height, not warning thresholds.` Reject nonfinite/nonpositive fixed values without losing the prior valid setting.
- [ ] Route each listed graph through the resolver. For Legacy pass the exact previous scale inputs (50 or 100 KiB/s floor, appropriate visible series, existing gauge denominators). Do not globally normalize away old differences while Legacy is selected.
- [ ] Adaptive uses only displayed samples. A chart that draws only download must not scale from hidden upload; new paired graphs use both. Bar gauges use the same adaptive/fixed denominator for down/up. Stacked plots use sums. Keep scale labels in network bit/sec formatting.
- [ ] Test all six view entry points with identical fixtures and configured display-series behavior. Assert opt-in behavior, app/per-widget precedence, correct bits/bytes conversion, latest sample/eviction recomputation, and no mutation of amber/red settings.
- [ ] Keep the actual rolling history as the viewport. There is no time-scroll integration to add now. Unit tests of the pure resolver prove changed ranges; vertical gallery scrolling must not rescale unchanged data.
- [ ] Run `swift test --filter NetworkGraphIntegrationTests`, then manually exercise Legacy→Adaptive→Fixed→Legacy and relaunch in an isolated preference profile. Verify rates and warning colours do not change merely because the scale mode changes.

## Task 6 — Build Cards and a shared size resolver

**Files:** create `Views/Widgets/CardWidgetContentView.swift`, `Views/Components/WidgetLayoutMetrics.swift`; modify `CustomModularWidgetView.swift`, `App/WindowManager.swift`; create `WidgetLayoutTests.swift`.

**Interfaces:** `CardWidgetContentView(config: CustomWidgetConfig, data: [WidgetChartData], palette: WidgetChartPalette, onClose: () -> Void)`; `WidgetLayoutMetrics.size(config: CustomWidgetConfig, itemCount: Int, gaugeRowCount: Int) -> CGSize` shared by editor and NSWindow. Retain the public `CustomModularWidgetView.effectiveSize` by forwarding to the resolver.

- [ ] Implement the spec's 160×160, 336×160 and initial 336×336 layouts, 20 pt corners, 14 pt padding and 11/26 pt typography. Cards inherit configured accents and theme. Classic retains its existing dimensions/layout calculation except needed unclipping fixes for selected gauge/numeric content.
- [ ] Preserve every supplied header token. Cards place customized tokens in a wrapping token row rather than silently removing them; include that row in size calculation. Empty default token sets use the standard metric header.
- [ ] Handle min sizes, long values, target names and multi-item row overflow. Use supported style/size choices rather than forcing two throughput labels into a 160 pt ring layout. Numeric Only reclaims graph space. Four process gauge rows must fit at computed size.
- [ ] Keep `CustomWidget_<UUID>` identifiers and existing placement manager. When content size changes, preserve the previous top-left anchor and clamp to the visible screen frame. Resizing/previewing must not reset positions or spawn duplicate windows.
- [ ] Test all size/presentation/style combinations offered by the UI, mixed grids at maxRows/maxColumns, long token rows, and changing graph style while window is shown. Preview and desktop sizes must match exactly.
- [ ] Run `swift test --filter WidgetLayoutTests`; capture native Cards and Classic comparison sheets. Manually test drag feel, close/reopen, opacity, click-through, floating/desktop layers and multiple displays. Use native window capture for AppKit effects, not ImageRenderer claims.

## Task 7 — Add the gallery and editor controls

**Files:** create `Models/WidgetGalleryPreset.swift`, `Views/Settings/WidgetGalleryView.swift`; modify `CustomWidgetSettingsView.swift`; create `WidgetGalleryTests.swift`.

**Interfaces:** `WidgetGalleryPreset.makeConfig(id: UUID, target: WidgetTargetScope) -> CustomWidgetConfig`; six stable preset identifiers: cpuHistory, memoryRing, diskCapacity, diskActivity, networkActivity, gpuHistory. `WidgetGalleryView(onAdd: (CustomWidgetConfig) -> Void)` owns draft selection only.

- [ ] Add an adjacent `Widget Gallery…` button; retain `New Custom Widget`. Group previews by Processor, Memory, Disk and Network. Show Small/Wide/Large only where relevant.
- [ ] Use fixture data for thumbnails with `Sample preview`; use the production Cards content view for appearance. Selecting a preset/target changes draft state only. No timer, live collector, UserDefaults write or window opens from a thumbnail.
- [ ] Require a concrete available disk/interface for target-specific presets; keep unavailable choices explicit. Prefer the current primary adapter for Network Activity but persist the selected adapter name. If the user chooses global, label it All interfaces. Do not pretend global is just the named primary.
- [ ] `Add Widget` generates a fresh UUID, appends once via preferences, selects it in the existing editor and shows its native window. Cancel leaves configurations byte-for-byte equivalent. Prevent repeated handling of the same button activation.
- [ ] Add Cards/Classic presentation picker, compatible metric choices, exhaustive style picker with disabled explanations, and inherited/per-widget scale controls. An unsupported saved choice remains visible with its reason until the user changes it.
- [ ] Test cancel/no-write, one Add/one record, duplicate distinct IDs, full property preservation, unavailable target handling, all six preset mappings, and scale default rules. Run `swift test --filter WidgetGalleryTests`.

## Task 8 — Complete integration evidence and handoff

**Files:** update both `FEATURE_GUIDE.md` and `docs/FEATURE_GUIDE.md` where widget/scaling claims appear, `docs/planning/DECISIONS.md`, `CHECKPOINT.md`; add `docs/runbooks/widget-rendering-verification.md` with the proven fixture/snapshot commands. Retain sanitized provenance under `docs/planning/evidence/`.

- [ ] Run the new targeted tests, then the full `swift test` in a verified isolated environment, and `swift build -c release`. CI's existing `.github/workflows/ci.yml` uses `swift test` followed by `Scripts/build_app.sh`. Use the existing packaging command only when ready for local native QA; it replaces `dist/PerfMon.app`, so use the isolated checkout and do not install over the user's app.
- [ ] Complete the acceptance matrix below with actual pass/fail/not-run and evidence paths. Include all selectable style×data combinations, not just attractive preset examples. Review the full native snapshot contact sheet and exercise real desktop behavior.
- [ ] Verify a pre-feature saved-settings fixture loads without altering IDs, positions, tokens, colours, dimensions, enabled states or menu-bar configuration; new configs round-trip and duplicate correctly. Before/after screenshots establish Classic continuity.
- [ ] Measure the same sanitized workload before/after: one Classic window, six Cards, and a multi-item dashboard for 60 seconds each at the same cadence. Record CPU/memory and sample counts. Investigate sustained extra CPU above 2 percentage points or unexplained memory growth; use relative evidence, not README performance claims as measurements.
- [ ] Update the feature guides: distinguish Bar Gauges from Ring Gauge; describe 60 samples accurately; document optional adaptive scaling and its separation from warnings; explicitly state unsupported daily usage/battery/temperature features are not offered.
- [ ] Freeze the implementation artifact (commit SHA or SHA-256 of the exact diff plus added files), record verified implementation/testing runtime metadata and commands, and request independent final review only under authorized reviewer routing. Without that authorization/capability, mark review pending. A source change after review invalidates the review.
- [ ] Refresh checkpoint and decisions before any authorized commit/PR. No remote action is implied. Report implementation, automated tests, local runtime QA, independent review and publication separately.

### Acceptance matrix

| Area | Required evidence |
| --- | --- |
| Legacy preservation | Old JSON fixture plus Classic before/after images; positions and preference values equal. |
| Chart coverage | Eight styles; every enabled metric combination; all eight scope cases for four legacy styles; capability reasons for unsupported combinations. |
| Edge datasets | Empty, one sample, zero, flat, spikes, NaN/inf, negative, mismatched pairs, process >100%, unavailable, reconnect. |
| Adaptive network | Upload-only; shared pair ceiling; stack-sum peak; visible-range expiry; legacy default; fixed overflow; unit conversion; alerts unchanged. |
| Appearance | Light/dark/system; 1×/2×; all offered sizes; long target/token/value strings; no clipped controls or text. |
| Native lifecycle | Gallery cancel/add/duplicate; preview vs desktop; show/hide/delete; drag; click-through; layering; size change; relaunch; multiple displays. |
| Collection/performance | One sample per target/tick; no per-card timers; bounded memory; paired histories aligned. |
| Release boundary | Local artifacts identified; no claim of installed/published success; independent review status explicit. |

## Implementation order and proportional stopping points

Tasks 1–4 establish truthful data and renderers; Task 5 applies scale behavior; Tasks 6–7 expose the additive visual feature; Task 8 proves integration. Each task ends with its tests and a checkpoint update. Do not publish a half-built gallery with unsupported graphs exposed as working choices. If one task reveals a material collector/platform gap, document it and continue independent tasks; do not invent values or silently broaden scope.

This plan has been self-reviewed against the design for scope coverage, interfaces, compatibility, input boundaries and evidence limits. That self-review is not an independent implementation review, and the planned tests have not run during planning.
