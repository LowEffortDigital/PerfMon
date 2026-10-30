# Independent PerfMon 1.5.1 followup review

Reviewer runtime independently verified with metadata-only `runtime_provenance.py`: gpt-6-astra / medium, provider openai, CLI 0.166.0-alpha.5.1. Review date: 2026-10-30. No source edits, test execution, delegation or remote actions by this reviewer.

Artifact: `release-v1.5.1-source.json`, SHA-256 `68c2b7da5721211ed75511af1406d57fd11bb410c5d82bf9663965f2045a5a13`. Independently verified all 102 listed file hashes and the SHA-256 of the canonical compact sorted JSON file map before and after final review. Baseline: merged commit `82166e1c743451721a5a05665d55db4ee797c237`. Scope is the followup delta, supported by the earlier integration/source reviews; this is not a new exhaustive audit of all unchanged code.

## Result

No blocking defect identified in this delta. Suitable to continue the authorized release path subject to successful build, CI and actual publication verification. This report does not establish deployment or published asset identity.

## Reviewed behavior

- The guarded computed `customWidgets` setter rejects edits before changing its private published backing array, writing defaults or sending the custom notification. This avoids the attempted rollback through a reentrant published setter. Initialization assigns the backing array directly, retaining saved archive bytes on both successful and failed decode. Existing consumers observe the preferences object; no projected customWidgets publisher dependency was found.
- Management methods guard load errors. Settings and process-menu controls disable creation, and WindowManager rejects presentation during load error, closing the factory-return ghost-window path. Recovery remains an error state; this change does not implement archive repair.
- Healthy-archive regression checks byte-preserving initialization followed by an indexed edit, ObservableObject publication, custom notification and saved decoded title. Corrupt-archive regression covers direct append, management methods and factory paths while asserting original bytes remain intact.
- Process history identities now include normalized bundle identity and hierarchy consistently for scope and multi-object items, preserving the prior unbundled key. Collector regression checks distinct bundle histories and deduplication of repeated identical targets. Existing ProcessTelemetryResolver matches name OR bundle; strict bundle-only telemetry isolation is not established by this patch or its fixture and remains outside this followup scope.
- Legacy automatic network scopes and mixed network dashboards expose per-widget scaling using the same scope classifications as rendering. Gallery selection resolves the clicked metric before updating state and retains a valid target on reselection.
- Info.plist and website metadata identify 1.5.1, build 8. The versioned notes file matches the unchanged tag-triggered workflow lookup. Use a fresh ordinary v1.5.1 tag; prior caution against the unchanged force-tag distribution helper remains applicable.

## Verification boundaries

Implementer reports the frozen artifact's full suite completed with 68 executions, 67 passes, one optional workload skip, zero failures and no exclusions. This reviewer inspected the tests but did not rerun them. Build and refreshed workload verification were pending when this review was requested. Record their receipts and real Swift 5.10 CI separately. Prior physical multi-display, manual click-through/layer and exhaustive Classic screenshot limitations are not resolved by this source review.

Any change to a manifest-covered file invalidates this review binding. Planning/evidence publication receipts may be updated separately without implying product artifact changes. No credentials, raw logs or user preference values are retained in this report.
