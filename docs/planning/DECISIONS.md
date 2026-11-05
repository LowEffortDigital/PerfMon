# PerfMon widget decisions

Record date: 2026-10-29. Keep entries sanitized; user corrections supersede proposals without erasing the reason for the previous decision.

| ID / state | Decision and rationale | Evidence / next action |
| --- | --- | --- |
| W01 — agreed | New widget designs are additive. Preserve the existing widgets and settings. | User request; acceptance contract in the design. |
| W02 — agreed | Astra prepares detailed planning; GPT-6.1 Sol implements. | Latest user instruction following disclosure of actual Astra/xhigh runtime. Implementation runtime is not yet active or verified. |
| W03 — observed | Correct repository is PerfMon, even though the chat initially opened in EventViewer. | `/Users/james/PerfMon/README.md`, custom widget/render/network source files. No EventViewer/LowEffort edits. |
| W04 — proposed | Extend custom widgets with Classic/Cards presentation and an opt-in gallery; retain native AppKit desktop windows. | Reuses persistence, telemetry and placement; avoids whole-app restyling or a second widget platform. |
| W05 — proposed | Add History Bars, Stacked History Bars, Ring Gauge and Dual Area, with an explicit metric compatibility matrix. | Reference images plus source's four existing styles. No silent fallback for unsupported styles. |
| W06 — observed | Multi-item widgets route every non-Sparkline selection through Grid Chart. Target-specific histories can fall back to globals. Shared custom history keys can be appended repeatedly per tick. | Source inspection recorded in design; Sol must reproduce with fixtures and verify corrections. No live reproduction claimed. |
| W07 — derived | Adaptive network scaling means visible graph-domain scaling, separate from warning colours. | Screenshot 3 wording and existing code's alert threshold settings. Proposal keeps alerts unchanged and makes new scaling opt-in. |
| W08 — proposed | Adaptive domain uses visible plotted peak, 10% headroom, upward 1/2/5 bps rounding and a 1 Kbps idle floor; paired directions share a scale. | Exact examples and tests in design/plan. These are design choices, not discovered current behavior. |
| W09 — observed | Histories contain 60 samples and configurable sampling cadence; unsupported metrics include battery, temperature and archived daily traffic. | SystemMonitorEngine, SystemMetrics and monitor inventory. New UI must not claim unsupported timing or metrics. |
| W10 — proposed | Use a pure fixture data/render boundary and common size resolver; fix duplicate configuration copying. | Enables native render evidence without starting singleton collectors or changing user preferences. |
| W11 — open | Global interface aggregation can include VPN/bridge traffic; primary interface is discovery-based. | Source observation only. Reproduce separately before changing aggregate counters or route selection; label new aggregates All interfaces. |
| W12 — open | Independent implementation review and native QA are pending. | No subagent dispatched; no product code built/tested by this planning pass. Sol must record immutable artifact and actual reviewer/test evidence. |

## Superseded direction

The initial turn paused for a Sol runtime switch. The user's subsequent instruction assigned planning to Astra, so this pass produces planning documents and a conceptual preview only. It does not claim the runtime switched or that Sol implemented anything.

## Evidence limits

Source findings are bound to `a6c637637a8934f1d63682e5cd7c712211930b85`. The preview contains deterministic synthetic data. Document checks and preview interaction checks are not app graph tests, live monitoring proof, independent review, installation or publication.

## Sol implementation checkpoint — 2026-10-29

- Agreed: user approved GPT-6.1 Sol / medium implementation; metadata-only probe verified that runtime.
- Observed: Cards/gallery, eight-style rendering, scale settings/resolver, compatible decoding, full-field duplication, startup validity, GPU availability, target-data safety, reconnect cleanup and per-tick history deduplication implemented locally.
- Ruling: retain the live adapter on CustomModularWidgetView, exposing pure CardChartData to rendering tests, instead of the larger telemetry-snapshot extraction. Reason: preserve the current observed view/engine boundary. Cost: full source/target fixture coverage remains pending.
- Ruling: preserve Classic layouts and former scale inputs as Legacy defaults; selected-style dispatch and target-data correctness are explicit exceptions. Cost: legacy domain differences remain until the user opts in.
- Observed: native ImageRenderer placeholders prompted SwiftUI gauge drawing and moving the drag handle to the observed window boundary.
- Observed: full suite's live updater assertion also fails on untouched baseline. Updater production code remains outside this feature.
- Pending: complete acceptance matrix, hands-on desktop/performance QA and independent final review. No reviewer was delegated; no release claim is made.

Native GUI inspection was attempted using a separate QA bundle, `.build/WidgetQA/PerfMonWidgetQA.app`, identifier `com.loweffort.perfmon.widgetqa`. It was built and launched without replacing the installed app. Computer-use inspection failed with macOS ScreenCaptureKit SCStreamError -3811 (capture stream could not start). Hands-on UI checks therefore remain blocked; no drag/click-through/multi-display claim is made.

## Final continuation decisions — 2026-10-29

- Agreed: user authorized a separate independent reviewer. Reviewer used Sol / medium and verified the final source/test digest before and after inspection.
- Superseded: earlier decision to defer the snapshot adapter. Pure value-copy snapshot/data resolver and collector are now implemented and fixture-tested.
- Observed: independent findings drove regression fixes for stacked CPU double-counting, paired bars, Classic scale overrides, missing-device fallbacks, process gauge units, historical gaps and stacked overflow. Final source review has no remaining material finding.
- Ruling: preserve finite Classic chart geometry and styling while introducing missing-sample path breaks. This keeps the existing visual baseline for measured data and prevents reconnection lines across unavailable intervals. Cost: gap-only behavior intentionally changes as specified.
- Ruling: retain the pre-existing live updater whitelist assertion outside this widget change; reproduce and disclose it rather than modify updater tests/product code. Cost: the unfiltered suite is not wholly green.
- Observed: three native 60-second workload scenarios passed the planned 2-percentage-point CPU-regression gate; no interval showed resident-memory growth.
- Blocked: capture still fails after unlocked-display confirmation. Automated native properties are evidence; they do not substitute for manual pointer feel or physical multi-display inspection. Only that manual acceptance remains pending.

## Gallery and desktop QA continuation — 2026-10-29

- Agreed: replace the preset dropdown with a selectable two-column visual gallery. Disk/adapter source selectors remain conditional. Six sample previews are isolated from live telemetry.
- Agreed: keep the grab point inside the visible Card header. Cards opt into native dragging with a header handle excluding the close control; Classic retains its existing path.
- Observed: user confirmed physical dragging follows the pointer normally. Synthetic dragging did not move the window and is not counted as a pass.
- Superseded capture block: foregrounding the QA app restores capture; hidden off-display preview geometry caused the diagnosed failure despite TCC Allow. User explicitly authorized `open` activation of only the QA bundle. No privacy settings changed.
- Observed: live QA covered all eight graph styles, adaptive/fixed scaling, gallery select/add/cancel, close/show and restart persistence; Classic received a render smoke check. Physical multi-display, manual click-through/layer interaction and exhaustive baseline screenshot comparison remain unverified.
- Observed: installed preferences canonical hash matched before/after QA. Built-in CPU/Memory/Disk source preservation remains byte-for-byte; no installation or publication.
- Observed: independent Sol6.1/medium review rebound to source/test digest 1859f45631527866775f9ac0f102c0e2b9ed5b26981b2964cf6171bf4855d8db with no material findings. Minor target-reset behavior on reselecting a device gallery tile is deferred.

## Release authorization and integration — 2026-10-29

- Agreed: user requested PR, merge and deploy. This authorizes the existing source/distribution/website release pipeline; installed preferences remain protected.
- Observed: origin/main advanced to b5c041e (v1.4.2), adding theme/launcher/error-state changes overlapping the widget implementation. Integrate rather than overwrite concurrent work; renew tests and independent review on the combined artifact.
- Derived: use v1.5.0, build7 for this additive feature release after v1.4.2/build6. No existing tag will be moved or overwritten.
- Observed: full local suite reproduced the obsolete updater version whitelist failure. Replace the release-specific whitelist with semantic ordering/valid-version assertions; preserve real asset/update checks. No updater production change needed for that failure.

- Observed integration corrections: Cards now use live persisted configuration consistently; pure disk resolver preserves partition/name aliases with actual disk histories. Added regressions failed before corrections and pass after them. Full suite63 executions/62pass/1optional workloadskip with no exclusions.
- Agreed release path: use a fresh ordinary v1.5.0 tag after successful PR CI/merge; avoid unchanged legacy force-tag helper. Verify downstream release/website outcomes separately.

- CI run40999282613 failed compilation on Xcode15.4/Swift5.10: process Disk I/O gauge ceiling inferred Int from chained integer arithmetic. Local Swift6 accepted it. Explicit 100.0 floating-point constant preserves the numeric value and makes the code compile with the supported CI toolchain; renew artifact review/CI before merge.

- Observed post-merge automated feedback required a followup before publication. v1.5.0 workflow cancelled before publish; tag kept immutable. Use 1.5.1/build 8 with a new reviewed PR.
- Derived fixes: shared bundle-aware process history/item identity with nil-bundle key compatibility; guarded backing storage and disabled mutation UI for unreadable archives; effective network-scope scale controls; clicked-metric gallery target resolution retaining valid reselect target. Existing process matching semantics unchanged.
- Observed test-discovered reentry in first observer rollback fix; replaced by computed guarded setter/private Published storage. Astra/medium independent review escalated after Sol failure per routing instructions.

- Observed final publication: v1.5.1 source/distribution downloads match digests and inspected bundle; all source/release/website workflows succeeded and live website version checked. No installed app update/settings changes.
- Observed limit: copying published executable into QA wrapper succeeded, but Mac locked before native capture; candidateQA and downloaded-bundle verification remain distinct. Publication is complete; optional exact-binary UI smoke remains pending unlock.
- Durable release receipts archive on the followup branch after merge; product main/tag remain at reviewed release. Existing redundant website release triggers all succeeded, with no workflow/configuration changes in this task.

## Followup enhancements and crash mitigation — 2026-11-03

- Observed: Menu bar renderer crash on background/sleep-wake due to nil CoreText dictionary copy of dynamic catalog colors (`NSColor.windowBackgroundColor`). Resolved by pre-rendering icons to 2x Retina `NSBitmapImageRep` with thread-safe caching and true knockout alpha blending.
- Agreed: Add drag-to-reorder for custom menu bar layout and widget settings. Implemented horizontal drag gestures with live step thresholds and visual feedback in `CustomTokenStripView`, `CustomWidgetSettingsView`, and `WidgetPreferences.moveCustomWidget`.
- Agreed: Add enable/disable feature and GUI addition modal for startup items. Implemented `StartupManager`, interactive toggle switches in `StartupAppsTabView`, and `AddStartupItemSheet` for selecting apps/scripts and registering macOS Login Items or User LaunchAgents.
- Observed: Stale SMB/TimeMachine network volume mounts cause macOS VFS calls (`mountedVolumeURLs` / `statfs(path)`) to enter uninterruptible kernel sleep (`U` / `?E`). Replaced blocking volume discovery in `DiskMonitor.swift` with `getfsstat(..., MNT_NOWAIT)` and added a 2-second timeout + background dispatch to `StartupManager.swift` and `SystemMonitorEngine.swift`.
- Agreed: User authorized PR, merge, and deploy for `v1.5.2` (build `9`).
- Tested: 70 unit tests passing, release build and packaging succeeded in `dist/PerfMon.app`.
- Published: PR #12 merged (`5c3d6de`), `v1.5.2` released on `Facelesswhite/PerfMon` and `LowEffortDigital/PerfMon`, website synced.

## Main window close/reopen and Dock icon lifecycle — 2026-11-04

- Observed: Closing the main window while any `DesktopWidgetWindow` was active left `hasVisibleWindows == true` (`flag == true` in `applicationShouldHandleReopen`), preventing Dock clicks or reopening the app from restoring `mainWindow`. Additionally, `DesktopWidgetWindow` had `canBecomeMain == true` and `canHide == true`, and `NSApp` remained in `.regular` policy when the main window closed.
- Agreed: Closing the main window (red `X` or `⌘W`) hides the main window and removes the app icon from the Dock (`NSApp.setActivationPolicy(.accessory)` by default via `hideDockIconWhenClosed = true`) while keeping the Menu Bar item and Desktop Widgets active. Reopening via the Menu Bar, double-clicking or right-clicking any widget, or opening the app again restores the currently running process's main window and Dock icon.
- Tested: 71 unit tests executed (70 passed, 1 optional workload skipped, 0 failures), including `testMainWindowCloseDockPolicyAndReopenFromMenuWidgetAndApp`.
- Agreed: User authorized PR, merge, and deploy for `v1.5.3` (build `10`).
- Published: PR #13 merged (`8eea799`), `v1.5.3` released on `Facelesswhite/PerfMon` and `LowEffortDigital/PerfMon`, website synced.

## USB & Thunderbolt electrical current draw telemetry — 2026-11-04

- Observed: On modern macOS / Apple Silicon (`IOUSBHostFamily`), `IOUSBHostDevice` exposes allocated current via `"UsbPowerSinkAllocation"` and capability via `"UsbPowerSinkCapability"`, while port-level allocations and wake/sleep limits (`"kUSBBusCurrentAllocation"`, `"kUSBWakePortCurrentLimit"`, `"kUSBSleepPortCurrentLimit"`, `"usb-port-current-limit"`) reside on the parent `AppleUSBHostPort` in the `IOService` plane. Additionally, `scanThunderboltDevices()` treated external `IOThunderboltSwitch` devices (`Depth > 0` / `Route String > 0`) as internal root bridges without power/current telemetry or tunneled PCIe disk correlation.
- Agreed: Extract USB current and port limits from both `IOUSBHostDevice` and its parent `AppleUSBHostPort`, extract external Thunderbolt / USB4 switch current and `IOPort` Type-C sink status, correlate tunneled PCIe NVMe disks, and display `mA` and `W` in the Devices KPI card, table column, and technical details tray.
- Tested: 72 unit tests executed (71 passed, 1 optional workload skipped, 0 failures), including `testUSBAndThunderboltCurrentDrawExtraction`.
- Agreed: User authorized PR, merge, and deploy for `v1.5.4` (build `11`), plus deprecating older `v1.0.0` and `v1.1` releases on the deployment repository.


