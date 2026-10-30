# Additive PerfMon widget gallery and graph correctness

Status: detailed proposal for GPT-6.1 Sol / medium implementation. Planning only; no product code changed.

Evidence date: 2026-10-29, America/New_York. Source baseline: `main` at `a6c637637a8934f1d63682e5cd7c712211930b85`, initially clean.

## Intent and boundaries

Add an attractive, optional collection of compact desktop widgets inspired by the supplied screenshots: rounded cards, restrained titles, prominent values, dense vertical histories, rings, and clearly paired throughput measures. Keep current widgets, the custom builder, saved positions, user colours and menu-bar configuration available as they are.

The user requested additive changes, functioning graph types, and review of adaptive network thresholds. The latest instruction assigns detailed planning to Astra and implementation to Sol. The screenshots are visual references, not instructions to reproduce another app's navigation, branding, notification-centre integration, or explanatory copy.

This proposal targets `/Users/james/PerfMon`, verified by its README and widget sources. EventViewer and LowEffort are outside the implementation scope.

## Observed current state

These are source observations, not running-app verification:

| Evidence | Consequence |
| --- | --- |
| `Package.swift`: Swift tools 5.10, macOS 14, executable and XCTest targets, no external packages | Keep the deployment floor and current dependencies. README's Swift 6 description is not the package requirement. |
| `CustomWidgetModels.swift:340`: Grid Chart, Sparkline, Bar Gauges, Numeric Only | Four existing styles must remain decodable and selectable. |
| `CustomModularWidgetView.swift:270–348`: multi-item scopes bypass the style switch; only Sparkline differs | Bar Gauges and Numeric Only currently become grid charts in multi-item widgets. Fix explicit dispatch. |
| `CustomModularWidgetView.swift:355–448`: disk/network headers can show combined speed while graph data is read/download only; missing histories can fall back to global data | Give every plot a truthful series label and never substitute a different target's data. |
| `NetworkWidgetView.swift:61–73`, `NetworkDetailView.swift`, HUD and performance sidebar: usually 100 KiB/s floor; `NetworkSectionView.swift:389–436`: 50 KiB/s floor | Automatic scaling already exists, but not as a consistent setting. Legacy behavior must be retained by default. |
| Custom network Bar Gauges use different fixed denominators for down and up | Adaptive comparison needs a common scale; old denominators are legacy behavior, not known link capacity. |
| `SystemMonitorEngine.swift:169–273`: histories appended inside the loop over configurations, with shared history keys | Multiple widgets referring to one key can append more than once per sampling tick. Deduplicate collection. |
| `MetricHistory`: 60 values, initially zero-filled; refresh interval is configurable | Do not label new histories as 60 seconds or treat startup padding as measured observations. |
| `WidgetPreferences.swift:792–804`: duplication reconstructs a configuration and omits maxColumns/maxRows | Copy all configuration fields while assigning a new identity. |
| `NetworkMonitor`: current counters and rates; no daily usage archive, battery, or temperature collector | Do not ship unsupported reference metrics or imply OS counters are daily/app-session totals. |
| Existing XCTest cases use shared preferences and start some live services | New rendering/scale tests must use fixtures; verify test preference-domain isolation before running the full suite locally. |

## Approach

Considered: restyle all existing widgets; add an independent second widget application; extend the existing custom-widget system with opt-in cards. Recommend the third: it reuses telemetry, persistence, placement and the existing editor without replacing established widgets or maintaining a second collector.

This is a proposed implementation decision, not an assertion that the user chose every detail below.

### Additive compatibility contract

- Add `WidgetPresentationStyle` with `classic` and `cards`. Missing saved field decodes as `classic`.
- Keep the four existing `WidgetChartStyle` raw strings byte-for-byte unchanged. Append new values; never rename serialized cases.
- Existing `CustomWidgetConfig` records preserve IDs, enabled state, scope, header tokens, dimensions, layout limits, colour overrides and position keys. No bulk migration or default gallery creation.
- Leave the built-in CPU, Memory, Disk, Network and All-in-One window designs intact. Graph correctness fixes are narrow, explicit exceptions: honour selected styles, represent unavailable targets accurately, sanitize invalid samples, and prevent duplicate history sampling.
- Add `Widget Gallery…` beside `New Custom Widget`. Opening/browsing the gallery causes no persistence or desktop-window changes. `Add Widget` appends exactly one fresh configuration and opens that widget through existing lifecycle code. Cancel writes nothing.
- Keep manual custom creation on the current Classic defaults. Gallery presets use Cards. Converting an existing widget requires selecting Cards or duplicating it first; conversion preserves ID and position.
- Existing network views and configurations retain legacy scaling until the user selects another mode. New network gallery presets explicitly use Adaptive Visible.
- Do not refactor navigation, process tables, topology maps, menu-bar/popover design, updater, distribution or branding assets.

## Visual specification

The default new presentation is **Cards**. Use existing SwiftUI/AppKit windows and the current theme/accent preferences. These are live native desktop windows, not WidgetKit extensions.

| Element | Proposed specification |
| --- | --- |
| Card surface | Quiet opaque surface by default; dark `#1C1D22`, light `#F8F9FC`; subtle neutral edge, no coloured outer glow; use semantic colour adaptations for contrast. |
| Shape and spacing | 20 pt outer corner radius, 14 pt internal padding, 8 pt content gaps. Multi-item inner tiles use 12 pt corners. |
| Typography | SF system; title 11 pt semibold, main value 26 pt semibold with monospaced digits, units/metadata at least 11 pt. Values never force units off-card. |
| Colour | Inherit each metric's configured accent. Down/read and up/write use stable distinguishable treatments plus labels; reserve amber/red for existing warning state. No new colour preference overwrites. |
| Small | 160 × 160 pt: ring or numeric single-metric cards. |
| Wide | 336 × 160 pt: histories, paired throughput, or numeric summary. |
| Large | 336 × 336 pt starting size: multi-item dashboard; grow using existing columns/rows settings and shared layout calculation. |
| Header | Metric icon and label, subtle drag affordance; controls visible on hover/focus with tooltips; preserve native drag behavior and close semantics. |
| Footer | Target and meaningful range/unit, e.g. `Last 60 samples` and `Scale 10 Mbps`. Long target names use end-preserving truncation and full hover disclosure. |
| Motion | Sample updates follow the existing sampling cadence. No independent per-card timer. Avoid decorative continuous animation; respect system reduced motion. |

Preset catalogue:

1. **CPU History** — wide vertical bars for total load, with optional stacked User/System history; value is total CPU percentage.
2. **Memory Ring** — small used/total memory ring with used and total bytes; no invented memory-pressure percentage.
3. **Disk Capacity** — small ring for the chosen mounted volume, with used/total capacity. Distinct from disk I/O.
4. **Disk Activity** — wide paired or stacked Read/Write histories, bytes/sec units.
5. **Network Activity** — wide dual-area Download/Upload chart, visible shared scale, chosen interface or explicit all-interface aggregate.
6. **GPU History** — wide utilization bars; unavailable hardware remains unavailable, not a convincing zero.

A multi-item Cards dashboard reuses these renderers. Gallery thumbnails use deterministic sample data labelled `Sample preview`; the selected editor preview and desktop window use the same live content component. No synthetic sample enters production history.

Not included: battery, temperature, daily/weekly/monthly traffic summaries, new notifications, native Notification Center widgets, menu-bar popover redesign. Those require separate data/platform work. The reference's refresh icon is not needed for continuously sampled windows.

## Graph contract

Separate data/units/availability from rendering. Every style consumes the same resolved target and values. Existing Classic typography and arrangement remain; new Cards get the new shell.

Add four styles: `History Bars`, `Stacked History Bars`, `Ring Gauge`, `Dual Area`. Do not imply that every style is meaningful for every metric.

| Data | Grid / Sparkline / History Bars | Bar Gauges | Numeric Only | Stacked History Bars | Ring Gauge | Dual Area |
| --- | --- | --- | --- | --- | --- | --- |
| CPU overall; memory%; GPU% | Yes, 0–100 | Yes, 0–100 | Yes | CPU User/System only, using real paired histories | Yes | Disabled: needs paired throughput |
| Disk capacity | Disabled until a real capacity history exists | Used/total | Yes | Disabled | Used/total | Disabled |
| Disk/network throughput | Yes; label plotted directions | Paired, labelled scale | Both directions | Sum of paired samples sets stack height | Disabled: no known utilization denominator | Yes, shared zero-based domain |
| Process CPU | Yes; keep >100% visible | Ceiling max(100 × logical cores, visible peak); label scale | Yes, can exceed 100% | Disabled | Disabled | Disabled |
| Memory used bytes | Yes, using byte history and physical-memory ceiling | Used/total | Yes | Disabled | Used/total | Disabled |
| Categorical memory status | Categorical stepped history/bands under existing styles; no fabricated percentage axis | Labelled status indicator | Status text | Disabled | Disabled | Disabled |

For mixed dashboards, offer a new style only if every item supports it. Existing saved incompatible combinations show an explicit `Choose a compatible graph` state on the affected item without altering the saved style. Never silently substitute another renderer. Existing four styles must work for all currently supported scopes, including multiDisk, multiNetwork, multiProcess and multiObject.

Pure render input carries series identifiers, labels, units, optional sample values, optional bounded total, scope availability and formatted readouts/gauge rows with individual units. Preserve existing Classic process CPU/RAM numeric summaries and its four distinct gauge rows; they are not all CPU measurements. Keep byte/sec values internally; use `formatNetworkThroughput` for new network axis/readouts so bits/sec conversion happens once. Never mix byte and percent histories. Existing memory status is derived from usage/swap, not a measured OS pressure percentage; label it as derived status in new Cards.

Handle empty and one-sample histories, constant zero, flat nonzero, maximum values, negative values, NaN/infinity, mismatched lengths, unavailable devices, and zero-size views. Clamp finite negatives to zero; preserve missing/nonfinite samples as gaps, not invented observations. Align shorter paired series by newest sample with missing leading positions. A single sample is a visible mark. An all-zero available series is distinguishable from no data. Numeric Only has no hidden chart or reserved chart height.

Do not use current global history for missing disk/interface/process history. A currently absent target shows its retained identity and `Unavailable`/`Not running`; historical values, if shown, are explicitly stale. Reappearing targets start a fresh contiguous segment, never bridge the absence with a line or compute rate across it.

## Adaptive network scaling

The screenshot describes graph autoscaling. It is separate from warning/critical colour thresholds. Existing `Pref_NetAmberMBps` and `Pref_NetRedMBps`, their defaults and their byte-based semantics stay unchanged. Explain in UI: `Adaptive scaling changes chart height, not warning thresholds.`

Introduce `NetworkGraphScaleMode`: `legacy`, `adaptiveVisible`, `fixed`. A nullable per-widget setting inherits an app setting whose missing/default value is `legacy`. Gallery Network Activity explicitly chooses `adaptiveVisible`. Fixed-mode UI accepts positive finite Mbps; store bytes/sec (`Mbps × 1,000,000 / 8`), default editor value 100 Mbps.

Adaptive algorithm:

1. Take only samples actually drawn in the visible history range and currently plotted series. Exclude other interfaces, hidden series, and samples outside the range.
2. Use the maximum of visible down/up values for overlaid/paired graphs. For stacked bars use the largest aligned down+up sum. For aggregates, sum only explicitly selected members at the same sample index; do not sum their individual peaks.
3. Sanitize inputs before arithmetic. Convert peak byte/sec to bit/sec for display-friendly rounding. Set target ceiling to `max(1,000 bps, peak × 1.10)`.
4. Round upward to the next `1, 2, 5 × 10^n` bit/sec step. Convert back to bytes/sec. Zero baseline, positive finite ceiling, bounded geometry. Guard multiplication/rounding overflow.
5. Recompute on sample append/eviction, target or series selection, and visible-range change. Lower the ceiling when the old peak leaves the range. No permanent all-time maximum, unrelated percentile cap, or arbitrary speed assumption.
6. Both directions in a chart use exactly the same ceiling. Show `Adaptive · scale …`; never call it connection utilization or link capacity.

Examples: down 1,000,000 B/s and up 250,000 B/s resolve to 10 Mbps (1,250,000 B/s). A stacked 1,000,000 + 500,000 B/s peak resolves to 20 Mbps. An empty/zero visible range uses a 1 Kbps ceiling while retaining the separate empty-vs-zero state. Fixed 10 Mbps clips a 20 Mbps sample with a visible overflow indication and retains the true numerical value.

Current graphs expose a rolling 60-sample series, not a horizontally browsable timeline. That full plotted series is their visible range. Vertical scrolling through cards does not change a card's time range. Do not add a history browser to satisfy wording in the reference. If a real time-range viewport is introduced later, recompute after its scroll settles using the range actually displayed; tests already cover explicit range changes.

Legacy mode supplies the original view-specific ceiling/floor and gauge denominators, preserving the unselected state. All existing network graph entry points receive a mode-aware adapter: Network widget, custom widget, All-in-One HUD, Performance sidebar, NetworkDetailView and NetworkSectionView. No colour-threshold change is coupled to this setting.

Network telemetry review also identified aggregate counters across multiple active interfaces and selection of a primary adapter by discovery order. VPN/bridge double counting and topology-change spikes require independent reproduction before collector changes. Label new aggregate cards `All interfaces`; prefer a concrete adapter for the gallery preset. Do not claim internet speed or route attribution.

## Verification and release boundaries

Acceptance requires fixture-based numeric tests, rendered native snapshots of every supported graph style, and hands-on desktop window checks. View construction or a successful compile alone cannot prove visible graphs work.

Keep Classic snapshot baselines and compare them after shared fixes. Review intentional differences separately: selected style now honoured, missing target now unavailable, invalid samples now safe, time captions truthful, duplicate-history sampling removed. Verify Cards in light/dark/system appearance, 1×/2× scale, each supported size, long labels, 0.5/1/2-second updates, relaunch and multiple monitors. New histories say `samples` unless timestamps are added and verified.

Apple documents [Canvas](https://developer.apple.com/documentation/swiftui/canvas) as immediate-mode drawing without element-level interaction; put controls and text outside drawing code. [ImageRenderer](https://developer.apple.com/documentation/swiftui/imagerenderer) supports SwiftUI image export; use it for pure chart fixtures and native-window capture for AppKit blur, drag and layering evidence. Do not require newer scroll APIs or raise macOS 14 for an unused history viewport.

No release, installation, updater action, tag, remote push, website sync, or distribution workflow is part of this planning pass. Later implementation reports must distinguish built, tested, exercised live, independently reviewed and published. Independent final review must bind to an immutable diff/commit digest; absent reviewer authorization, mark it pending.
