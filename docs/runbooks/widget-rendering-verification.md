# Widget rendering verification

Run from the PerfMon repository:

```sh
swift test --filter 'NetworkGraphScaleTests|WidgetChartRenderingTests|WidgetNativeLifecycleTests'
swift test --skip MacTaskManagerTests.testLiveAutoUpdateCheckAgainstDistributionRepo
swift build -c release
```

On 2026-10-29, 43 tests passed with the live updater case excluded. The excluded test's hard-coded release-version assertion also failed on untouched revision a6c637637a8934f1d63682e5cd7c712211930b85. The unfiltered suite is not green; updater code is unchanged.

The observed XCTest preference domain was com.apple.dt.xctest.tool, outside the installed app's com.loweffort.perfmon domain. New native lifecycle tests assert domain separation. Reverify if the harness changes; never reset installed app preferences for tests.

Rendering tests export deterministic 2× light/dark fixtures for every graph style to /tmp/perfmon-widget-native-renders. Final PNG copies are in ../planning/evidence/widget-renders. Inspect the images: encoding alone is not visual verification. Native AppKit wrappers can render as yellow ImageRenderer placeholders; the new pure card renderer uses SwiftUI gauges, with an optional Cards-only native drag handle over the header label, excluding the close control.

The native lifecycle test checks a real DesktopWidgetWindow showing/hiding fixture content. PERFMON_NATIVE_QA_HOLD_SECONDS optionally holds it for at most 60 seconds. This is not a performance benchmark or evidence of drag, click-through, layering or multi-display behavior.

Scale tests cover paired/stacked peaks, visible-range expiry, upload-only traffic, idle/nonfinite input, fixed/legacy modes, legacy decoding, complete duplicate-field preservation and startup-history validity. Legacy network adapters retain their old scale inputs. Adaptive mode changes domains independently of warning settings.

## Current verification and remaining acceptance

The final local suite has 58 passing tests and one optional workload skip after excluding the baseline live updater assertion. Run `PERFMON_RUN_WIDGET_BENCHMARK=1 swift test --filter WidgetWorkloadTests` for the separate three-minute workload comparison. Source-bound results are retained in ../planning/evidence/widget-workload.json. This compares native test-process resources at one-second cadence, not universal app CPU usage.

The pure snapshot resolver, collector, source/style matrix, shared geometry and layout are now implemented. Independent final review is in ../planning/evidence/independent-widget-review.md. Final source/test hashes are in widget-final-source.json; changes invalidate that review.

Native capture recovered by foregrounding the QA app. All eight graph styles were exercised live (seven network styles plus Memory Ring), along with gallery selection/add/cancel, close/show, relaunch persistence, adaptive and fixed network scaling, and a Classic presentation smoke check. The user confirmed physical dragging follows the pointer normally. Synthetic dragging was unsuccessful and is not a pass. Physical multi-display behavior, manual click-through/layer interaction and exhaustive Classic before/after screenshot comparison remain unverified. Automated NSWindow show/hide, click-through properties, floating/normal levels and restoration pass. The current preference model offers floating and normal levels; older guides describing a separate desktop layer are not current source evidence.

No installer, release tag, remote push, distribution workflow or website sync was performed. The isolated QA bundle is under .build/WidgetQA and uses com.loweffort.perfmon.widgetqa, separate from the installed app.

## Capture recovery for the isolated QA app

When ScreenCaptureKit reports -3811/-3812, first verify the intended QA window is visible on the active display. In this session TCC allowed capture, but WindowServer reported that the capture rectangle did not intersect a display. Hidden preview windows were marked onscreen outside display bounds. Foregrounding the QA main window restored capture; helper/replayd soft resets did not resolve this geometry issue. No privacy settings or TCC reset was necessary.

The user authorized activating only this local QA bundle:

```sh
open -a /Users/james/PerfMon/.build/WidgetQA/PerfMonWidgetQA.app
```

Reacquire the accessibility state after a window transition or capture failure before retrying any action. Do not blindly repeat Add. Native scrolling, keyboard shortcuts and synthetic dragging were unreliable in this environment. A stuck native Window menu was cleared by restarting only the exact QA executable. The user separately confirmed physical dragging works. None of these observations establishes physical multi-display or click-through behavior.

Keep QA preferences in `com.loweffort.perfmon.widgetqa`. Compare canonical sorted-plist hashes of `com.loweffort.perfmon` before and after QA without persisting its values. The recorded production-domain hashes matched. Gallery previews use sample data; retained screenshots should exclude process lists and target IP fields.

## PR and release path

Integrate current `origin/main` before release; the widget work began before v1.4.2's theme/launcher changes. Keep the built-in CPU/Memory/Disk views identical to current main when integrating additive Cards. Resolve conflicts without dropping observed preference refresh, target errors or dynamic colors. Full `swift test` now runs without updater exclusions: the live updater check validates semantic ordering and asset properties instead of a release-version whitelist.

For an authorized release, update Info.plist, website_release.json and release_notes_<tag>.md together; bind review to release-v1.5.0-source.json (or a new release manifest), run the full suite and build/package scripts, then merge only after CI succeeds. Create a new ordinary tag at the verified merged commit and push that tag. The `release-distribution.yml` tag workflow publishes source/distribution DMGs and dispatches the LowEffort website deployment. Do not use the legacy distribute_release.sh for this path: it force-replaces tags and has stale README metadata. Never move an existing release tag.

Verify the source and distribution release asset digests match; download and inspect the read-only mounted app's bundle version/build and signature. A successful release workflow is separate from the website's downstream workflow and live page version check. Keep the prior tag available for rollback; do not delete old assets or change installed app settings during release verification. Record PR, merge SHA, tag target, workflow IDs and asset digests in the checkpoint/receipt.

CI uses Xcode15.4/Swift5.10 even when the development Mac uses Swift6. Chained integer arithmetic passed to a Double gauge ceiling can fail inference on that compiler; type the first literal as floating-point (for example `100.0 * 1024 * 1024`). Validate with actual CI rather than treating a newer local compiler as toolchain parity.

Post-merge automated review found history identity collisions between process targets with different bundle IDs. Collection keys, resolver keys and multi-item IDs must share bundle-aware identity; preserve nil-bundle keys where practical. This does not change the existing name-or-bundle process matching policy. For unreadable widget archives, reject UI/model edits while preserving raw data. Do not roll back a Published property by assigning itself inside didSet: wrapper reentry can crash. Use private Published backing storage and a guarded computed setter, load the backing store directly, and test both blocked corrupt archives and healthy edit publication/persistence. A crashing synthetic corruption test may leave its test-domain payload behind; remove it only after an exact byte comparison, never reset the installed app domain.

v1.5.0 tag remains at its merged commit, but workflow40999883327 was cancelled before publication. Both repositories retained v1.4.2 as latest. Corrections use a new v1.5.1/build8 tag after a followup PR; never move an already pushed release tag. Publication manifests should contain only versioned artifact files, excluding local ignored Finder metadata. Verify manifest files directly against the merged commit before pushing a new tag, and stop the shell on any verification failure.
