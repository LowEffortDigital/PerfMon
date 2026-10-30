# Additive widget native QA receipt

Runtime: metadata-verified GPT-6.1 Sol / medium. Source/test digest: `1859f45631527866775f9ac0f102c0e2b9ed5b26981b2964cf6171bf4855d8db`.

Target: separate local `.build/WidgetQA/PerfMonWidgetQA.app`, preference domain `com.loweffort.perfmon.widgetqa`. Installed app was not replaced. These are implementer observations and the explicitly attributed user confirmation, not independent reviewer reproduction.

| Check | Outcome | Evidence scope |
| --- | --- | --- |
| Visual gallery | Pass | Six selectable sample previews, selected border/checkmark, conditional source selector; retained widget-gallery-native.png. Preset dropdown replaced. |
| Cancel / Add | Pass | Cancel creates no configuration; Add creates one live card in QA profile. |
| Grid Chart | Pass | Live paired network plot and numeric rates visible. |
| Sparkline | Pass | Live paired network traces visible. |
| Bar Gauges | Pass | Both throughput directions rendered. |
| Numeric Only | Pass | Live down/up values; graph intentionally absent. |
| History Bars | Pass | Both throughput directions visible. |
| Stacked History Bars | Pass | Live stacked paired throughput visible. |
| Dual Area | Pass | Live paired area traces visible. |
| Ring Gauge | Pass | Live Memory percentage, used/total and ring visible. |
| Adaptive network domain | Pass | Ceiling changed with visible traffic, common domain for paired directions. Peak expiry/idle/nonfinite/overflow edge cases additionally tested with fixtures. |
| Fixed network domain | Pass | Configured 100 Mbps domain shown on live Dual Area card; restored Adaptive afterward. |
| Close / Show | Pass | Desktop card hides and editor Show on Desktop state updates; reopening renders. |
| Restart persistence | Pass | QA CPU/Network configurations retained across QA process restart. |
| Physical drag | User-confirmed pass | User answered that the card follows the pointer normally. Header hit-region test separately passes. |
| Synthetic drag | Unsuccessful | No verified window movement; not used as evidence of physical failure or success. |
| Classic presentation | Smoke pass | New QA Memory card converted to Classic renders, then restored Cards. Not an exhaustive before/after baseline comparison. |
| Installed preference preservation | Pass | Canonical sorted-plist hashes match; values not retained. |
| Built-in CPU/Memory/Disk source preservation | Pass | Byte-identical to baseline revision. Shared network/domain changes retain Legacy defaults. |
| Click-through and window levels | Automated only | Native property/lifecycle assertions pass; physical pointer-through and layer interaction not verified. |
| Multiple physical displays | Not run | One active display available; simulated coordinate tests are separate evidence. |
| Exhaustive Classic golden comparison | Not run | Source preservation, fixture coverage and live smoke check do not replace a pre-feature screenshot matrix. |

Capture failure was recovered by foregrounding the QA app. Native input/capture failures during transitions required fresh AX state; no privacy settings or production preferences changed. QA app is left with the gallery open. No installer, release or publication.
