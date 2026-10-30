# Additive widget work: current checkpoint and Sol handoff

**Current status:** see the final verification checkpoint at the end of this file. Earlier entries are historical; do not replay completed work.

Updated: 2026-10-29, America/New_York.

## Current state

- Repository: `/Users/james/PerfMon`.
- Source baseline: branch `main`, HEAD `a6c637637a8934f1d63682e5cd7c712211930b85`; working tree was clean before planning.
- Planning runtime verified by installed metadata-only probe: `gpt-6-astra`, `xhigh`, provider `openai`, CLI `0.166.0-alpha.5.1`.
- User requested Astra detailed planning for a Sol implementer after runtime disclosure. Intended implementation runtime: `gpt-6.1-sol` / `medium`; no switch or implementation claimed.
- Only local planning/guidance artifacts were added. No product source, existing setting, installed application or release changed. No commit, PR, push, deployment or publication receipt exists for this pass.

## Read before implementation

1. [Decision record](DECISIONS.md)
2. [Design and scope](../superpowers/specs/2026-10-29-additive-widgets-design.md)
3. [Detailed implementation plan](../superpowers/plans/2026-10-29-additive-widgets.md)
4. [Planning provenance manifest](evidence/2026-10-29-widget-planning.json)

## What is complete

- Inspected README, feature guide, package/CI/release scripts, custom-widget model/editor/renderers, network collector and graph entry points, engine histories, preferences, window lifecycle and relevant tests.
- Mapped source-backed graph-dispatch, target-data, duplicate-history and scale-consistency issues.
- Defined additive Cards/gallery, eight-style compatibility, preservation defaults, pure adaptive scaling, six initial presets, bounded file-level tasks and acceptance checks.
- Prepared an interactive visual concept using sample data. It is conversation content, not native app code or live telemetry.

## Execute next

Start with Task 0 in the implementation plan. Probe actual runtime; read these records; verify branch/HEAD/status; preserve concurrent changes. Establish Classic baselines before changing the shared data/render path. Implement configuration/data/scaling/rendering first, then Cards/gallery, then run the full acceptance matrix.

Runtime approval and the existing additive scope are already in the conversation. Do not ask for them again without a new mismatch or material scope change. Any unapproved design choices in the spec remain clearly marked proposals; apply later user corrections before implementation. Do not delegate without authorization.

## Evidence and unresolved work

- Source inspection: complete at the recorded baseline.
- Document consistency/local links: checked before final handoff; source implementations remain unchanged. Manifest binds the final document content.
- Concept preview checks: inspected at 736 px and 320 px; adaptive steady/burst/peak-expiry/upload-only/idle states and fixed overflow exercised in the browser; no observed console errors. These checks apply only to the sample-data HTML concept.
- Swift build/tests during planning: not run.
- Native screenshot baseline, graph edge-case tests, live desktop interaction/performance: pending Sol implementation.
- Independent final implementation review: pending and must bind to the immutable reviewed artifact.
- Remote backup/publication: none. All planning records are local uncommitted files.

The next implementation checkpoint must distinguish observed facts, derived requirements, proposals and unresolved issues; record actual commands/outcomes and artifact digests. Refresh this handoff and decisions before authorized commits or PR updates. Do not put raw logs, user preferences, target identifiers, personal/customer data or credentials into these records.

## Implementation checkpoint — 2026-10-29

Branch: codex/additive-widget-gallery. HEAD/base remains a6c637637a8934f1d63682e5cd7c712211930b85. Changes are local and uncommitted. Actual runtime: GPT-6.1 Sol / medium.

Implemented: Cards/gallery and six presets; eight chart cases; legacy defaults; pure adaptive scaling and settings; capability checks; missing-target/GPU states; duplication; startup validity; history deduplication; adapter reconnect cleanup.

Verification: 43 tests pass with the pre-existing live updater test excluded. The same updater assertion fails on untouched baseline. Native light/dark fixtures cover all eight styles; native fixture-window show/hide passes. Release build succeeds.

Remaining: full target/style/source acceptance matrix; broader pure snapshot adapter proposed in Task 2; hands-on drag/click-through/layer/multi-display/relaunch checks; measured workload comparison; independent final review. The approved plan is not yet fully complete or release-ready.

Read docs/runbooks/widget-rendering-verification.md and implementation provenance before continuing. Installed app and distribution artifacts were not replaced. No commit, push, PR, installation or publication occurred.

Native GUI inspection was attempted using a separate QA bundle, `.build/WidgetQA/PerfMonWidgetQA.app`, identifier `com.loweffort.perfmon.widgetqa`. It was built and launched without replacing the installed app. Computer-use inspection failed with macOS ScreenCaptureKit SCStreamError -3811 (capture stream could not start). Hands-on UI checks therefore remain blocked; no drag/click-through/multi-display claim is made.

## Final verification checkpoint — 2026-10-29

Source/test digest: `979a86540caf425992fe4791206310c13e1dbf49a5b13bb4118accffded9e5e7`. Independent Sol / medium reviewer verified this exact manifest and reports no remaining material source findings. Read `evidence/independent-widget-review.md` and `evidence/2026-10-29-widget-final-verification.json`. Source remains local/uncommitted on codex/additive-widget-gallery at the original base revision.

Completed since the previous checkpoint:

- Extracted value-copy WidgetTelemetrySnapshot, pure WidgetChartDataResolver, CustomHistoryCollector, shared layout and chart geometry. The earlier adapter deferral is superseded.
- Added source/style/target fixture matrix and review regression tests: paired bars, CPU composition, all four process gauge units, visible-history gaps, target absence, disconnected histories, stacked overflow and per-widget domains.
- Local suite: 57 passed, one optional workload test skipped, zero failures. The existing live updater assertion is excluded; its failure was reproduced on untouched baseline. No updater production change.
- Opt-in three-minute workload test: passed separately on this exact source. Three 60-second scenarios: Classic 7.77% process CPU, six Cards 7.45%, dashboard 7.34%. Resident memory did not grow within the sampled intervals. These are test-process measurements, not universal provider/performance claims.
- Release build and code-sign verification of separate QA bundle passed. QA app refreshed at `.build/WidgetQA/PerfMonWidgetQA.app`; installed app and its preference domain remain untouched.
- Automated native show/hide, click-through property, floating/normal levels, restoration, gauge sizing and simulated screen-coordinate tests pass. CPU/Memory/Disk built-in view sources match the baseline byte-for-byte. Other built-in network layouts retain Legacy scale inputs; this is source-level preservation, not a golden screenshot comparison.

Remaining external acceptance gate: hands-on pointer/drag, real multi-display and visual Classic comparison. Native computer-use capture fails with ScreenCaptureKit -3811, including after the user confirmed the display is unlocked. Do not claim those manual checks passed. Reattempt them when native capture works; no implementation/model/reviewer approval needs to be requested again. Any code/test change invalidates the current source review until rebound to a new digest.

No commit, push, PR, installation, release tag, distribution or website publication was performed.

## Capture recovery and gallery steering — work in progress

Runtime reverified GPT-6.1 Sol / medium. Native capture succeeds when the QA main window is brought to the foreground. Read-only diagnostics showed TCC Allow and an active 3440×1440 display, while WindowServer rejected a capture rect outside all displays. Hidden app previews were marked onscreen at x=-300. Soft-resetting replayd and restarting only the Codex computer-use helper did not resolve it; foregrounding the QA window did. No privacy permission or installed widget setting was changed.

Hands-on Cancel creates no widget; Add creates a live QA-only CPU card; coordinate click on its close control hides it. Drag attempts are not yet a verified pass. Optional dedicated Card header drag surface added; native hosting hit-region regression test passes. Classic drag code is unchanged. This invalidates the prior source review digest; independent agent is reviewing the delta and must rebind when final.

Latest user steering: show a selectable widget-preview gallery instead of a preset dropdown. Implemented two-column previews with sample data and single selection, retaining disk/adapter source selectors. Gallery and drag changes: 58 tests passed plus one optional workload skip; release build passed. QA app rebuilt; foreground activation is pending explicit user response because capture bootstrap cannot select a hidden preview.

Installed production settings snapshot was read and canonically hashed privately before QA; no values were retained. Compare after QA. All created cards are in com.loweffort.perfmon.widgetqa, not com.loweffort.perfmon.

## Current handoff — gallery and desktop QA continuation

This entry supersedes earlier capture-blocked and drag-pending statuses. Branch remains codex/additive-widget-gallery, base a6c637637a8934f1d63682e5cd7c712211930b85; all changes are local/uncommitted. Runtime metadata verified GPT-6.1 Sol / medium.

Frozen source/test digest: 1859f45631527866775f9ac0f102c0e2b9ed5b26981b2964cf6171bf4855d8db (canonical compact sorted JSON file map). Independent reviewer verified this artifact before/after inspection, no material findings. Source/test changes require renewed review.

User steering implemented: selectable visual gallery instead of preset dropdown; Cards-only drag handle inside visible header, excluding close control. Gallery sample data does not invoke collectors. Physical card dragging is a user-confirmed pass; synthetic dragging remains unsuccessful.

Fresh verification: 58 tests pass plus one optional workload skip, zero failures; known baseline live updater assertion excluded. Release build passes. All eight styles exercised in live QA (Network Grid Chart, Sparkline, Bar Gauges, Numeric Only, History Bars, Stacked History Bars, Dual Area; Memory Ring Gauge). Fixed100Mbps and Adaptive visible scaling observed. Gallery selection/cancel/add, close/show and app restart persistence observed. Classic presentation smoke check renders and was restored to Cards. The separate QA app is left with the gallery open. Created CPU/Network/Memory configurations exist only in its separate domain.

Capture recovery: foreground QA app on active display. WindowServer capture-rectangle geometry, not permission denial, explained observed offscreen failures. No privacy settings changed. Read the capture-recovery section of the runbook before retrying. Native keyboard/scroll and synthetic drag automation remain unreliable; do not infer passes from attempted input.

Preservation: production com.loweffort.perfmon canonical preference hash unchanged; see installed-settings-preservation.json. Installed app, existing widget configurations and settings were not changed. No commit/push/PR/installation/release/publication.

Acceptance limits: only one active physical display available; physical multi-display, manual click-through/layer behavior and exhaustive Classic baseline screenshot comparison remain unverified. Automated lifecycle/coordinate checks do not substitute for these. Minor gallery device-target reset on reselect is deferred. Final workload result and source-bound execution receipt are recorded in 2026-10-29-widget-final-verification.json when complete.

Final workload on the current frozen digest passed: Classic 8.65%, six Cards 9.01%, dashboard 9.16% test-process CPU, all three 60-second intervals; Cards/dashboard differences remain below the 2-percentage-point gate. RSS changed +2.13MiB, +128KiB and -160KiB respectively; no long-run leak claim. Final machine-readable receipt and native QA matrix are in evidence/2026-10-29-widget-final-verification.json and evidence/widget-desktop-qa.md. All source/test hashes reverified after testing; no source edits after final review.

## PR/merge/deploy authorized — integration in progress

User requested PR, merge and deploy. Preparing a source commit before integrating origin/main b5c041e/v1.4.2. The pre-integration source review remains historical; combined artifact must be retested/reviewed. Full suite currently has the known updater whitelist failure. Planned release v1.5.0/build7 via existing tag-driven distribution and website workflow; no tag pushed yet. Prior manual QA limitations remain disclosed.

## v1.5.0 integration ready for PR

Current release manifest digest 0ca1cfba91c66d3a12a25804e25f5f40a2caea34637e5e9f7b149f74bb6f1136 includes integrated main code, tests, scripts/workflows and release metadata. Independent release review has no material blocker. Full suite63 executions/62pass/1optional workloadskip/zero failures, no exclusions; app build/package/signature verification passed. Native QA refreshed with integrated candidate: gallery displays, current custom-widget launcher opens live Network Dual Area with adaptive domain; installed preferences hash still unchanged. Workload is running; no merge/tag until final gates pass. Release-v1.5.0-execution.json records evidence. Publishing branch/PR next under user authorization.

Branch publication confirmed: origin/codex/additive-widget-gallery at integration commit 1a70ccf. Current-artifact three-minute workload passed; source-bound results in release-v1.5.0-workload.json and execution receipt. Source/test/release-metadata unchanged after independent review. Preparing final PR against main; CI/merge/tag remain pending.

PR receipt: https://github.com/Facelesswhite/PerfMon/pull/10, head fc5469d2d7751a860bb6f7495d3d44e0b206489a, base b5c041e3aa2de2bdba2a02d2e6ad5a29e9af2b19. GitHub CI run40999282613 queued. No merge/tag yet. Source artifact remains frozen/reviewed; this receipt is a local checkpoint update until separately committed.

CI compatibility correction: run40999282613 failed on Swift5.10 at the process disk gauge ceiling. One literal changed100→100.0 with unchanged numeric value. New frozen manifest9048ac6274524a61dd3e1b78c0ae0b96f31b3ca25f55906a05a081e2248888aa independently rebound/verified; full suite63 executions/1optional skip/zero failures. Prior workload/native smoke apply to the preceding numerically equivalent source and are explicitly identified in the execution receipt. Pushing compatibility correction and receipt; merge/tag remain gated on real CI.

Merge gate: GitHub CI40999550139 succeeded for exact PR10 head53b00a849fcc7408f87ec50c645428d0d51a9856 on Xcode15.4/Swift5.10; mergeStateStatus CLEAN, base unchanged b5c041e. Local app/DMG/signature also refreshed after compatibility edit. All release manifest hashes still match reviewed9048ac62... artifact. Proceeding with user-authorized squash merge; receipt update is local pending later publication.

## v1.5.0 cancelled; v1.5.1 followup in progress

Automated review comments were read during release verification. Confirmed history-key bundle collisions, blocked archive persistence while UI accepted edits, and missing per-widget scale controls on automatic network scopes. Cancelled release40999883327 before package/publish/distribution/website steps; both release repos still have no v1.5.0 release. Keep tag immutable at82166e1; next release v1.5.1/build8 via followup PR. Branch codex/widget-release-followup starts at origin/main82166e1. Existing local receipt edits carried forward safely.

Regression tests reproduced collision and lost-edit behavior before corrections. First Sol recovery guard recursively reassigned a Published observer; regression crashed in isolated XCTest domain. Replaced with guarded computed property over private Published storage and direct initializer storage load; original corrupt bytes retained. Removed only exact synthetic crash-leftover test payload from com.apple.dt.xctest.tool, after asserting bytes; installed domain untouched. Independent reviewer escalated under routing rule to metadata-verified GPT-6 Astra/medium; Sol remains implementer. Healthy archive publication/persistence regression added; full suite rerunning. Scope is history identity isolation, not changing pre-existing process resolver name-or-bundle matching semantics.

Followup artifact frozen68c2b7da5721211ed75511af1406d57fd11bb410c5d82bf9663965f2045a5a13 (102 versioned files). Independent Astra/medium delta review no blockers. Fullsuite68 executions/67pass/1optional workloadskip/no failures/no exclusions. Build/package/signaturepass. Native QA verifies automatic-network scaleoverridevisibility and live rendering, disk-gallery selectionenablesAdd. Installed preferences canonical hash unchanged. Current workload running; preparing followupPR. Receipt release-v1.5.1-execution.json.
