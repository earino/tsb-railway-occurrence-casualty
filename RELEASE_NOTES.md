<!-- dataset-factory:links -->
**Dataset Factory · v2026.10**

[Dataset website](https://earino.github.io/tsb-railway-occurrence-casualty/versions/v2026.10/) · [GitHub release](https://github.com/earino/tsb-railway-occurrence-casualty/releases/tag/v2026.10) · [Hugging Face](https://huggingface.co/datasets/earino/tsb-railway-occurrence-casualty/tree/v2026.10) · [Catalogue](https://earino.github.io/dataset-factory-site/)

Artifact: e75a59bb1116e39b980c9fa2f5ea7c2b984fa6ccce4b3286f194de69d2b32309

### Suitability and evaluation requirements

Retained for retrospective model-development research with documented timing limitations. No answer recovery was found in the bounded audit; historical forecasting validity remains unverified.

**Supported uses:** Retrospective train/validation model-development experiments under the documented timing and selection limitations; Final evaluation only under a protocol that isolates holdout data from development and prevents later rows influencing earlier predictions

**Not supported:** Claims of verified real-time or operational forecasting validity; Treating development/validation scores as untouched final-test results

**Evaluation access:** chronological. The labeled eval split is development/validation data. Keep the final holdout outside agent development; prevent later rows from influencing earlier predictions.

**Historical availability:** assumed. **Training-label timing:** unverified. **Vintage:** current snapshot. **Selection:** unverified.

**Cross-row audit:** bounded no recovery. Bounded attack search, 15 active minutes per repository, frozen plan; not a security proof Holdout recovery was not tested by this audit.

Structural qualification and baseline scores do not certify operational forecasting validity.

- The bounded leakage audit found no recovery path (0 covered by any tested join); a bounded search is not a proof. One calendar week straddles train/eval (5 eval rows).
- Historical feature/label availability at the declared prediction instant is assumed from current snapshots, not verified against release vintages.
- The current scorer passes the complete feature frame to predict_proba; prediction-time isolation is instructed but not mechanically enforced.
- Features come from a finalized occurrence register; excluding outcome taxonomy does not prove each retained contextual field was available at occurrence onset.

Review: 2026-10-24; applies to v2026.10 / `e75a59bb1116e39b980c9fa2f5ea7c2b984fa6ccce4b3286f194de69d2b32309`. Frozen data and tags are unchanged.
<!-- /dataset-factory:links -->

# Release notes - v2026.10

First release of **tsb-railway-occurrence-casualty**: a binary occurrence-level
prediction task on Canadian rail safety. When a railway occurrence begins on the
Canadian federal network - at a known place, time and operating context - will it
result in at least one casualty? Every feature is a pre-onset property of the
occurrence; TSB's own outcome-embedding taxonomy, injury fields, at-scene weather,
investigation and process fields are excluded by construction and never ship.

## What ships

| Asset | Bytes | Sha256 (first 16) |
| --- | ---: | --- |
| `public-train.csv` | 980,529 | `4a032f68c4ad944a` |
| `public-eval.csv` | 474,335 | `2f1dcafb6cc701b1` |
| `private-holdout.csv` | 244,586 | `b00bf00bd4f918ce` |
| `meta.json` | 2,606 | `5f3852f3150cd808` |
| `quality.json` | 7,131 | `a7343bd15a119c67` |

Artifact version `e75a59bb1116e39b...`, gate **1.4.0**, 37/37 checks, 0 failed,
re-accepted by `--accept` on the bytes before any scoring. Splits (temporal, by
occurrence date): train 2016..2021 (7,925 rows, 11.14% positive), eval 2022..2024
(3,831 rows, 12.09%), holdout 2025..2026-08 (1,976 rows, 14.07%) - both classes in
every window, each positive an independent occurrence.

## Task

A derailment, crossing accident, trespasser event, fire or collision begins at a known
subdivision, local hour and activity type, on a known railway. Predict, at onset,
whether anyone will be killed or injured. The casualty outcome unfolds after onset;
the label is derived from injury fields that are excluded from every shipped file.

## Baseline

The runner-contract baseline (the benchmark's own unmodified `train.py` + `validate.sh`)
measures **eval AUC 0.8126**, printed identically by both entry points. The
construction-time references it had to beat: a trailing-12-month casualty-rate
persistence analogue (0.527 eval / 0.495 holdout rank AUC) and the best single context
column, `activity_type` (0.707 / 0.699). The full model clears both - the task teaches
real pre-onset signal and is not a persistence exercise. All three numbers, with the
commands that produced them, are in `VERIFICATION.md`.

## Construction notes

- **The panel start is measured, not assumed.** TSB's pre-2016 rows carry a legacy
  taxonomy-era signature that fades gradually (2-7% through 2015, ~0 from 2017), so the
  panel starts 2016-01-01; the per-year decay table is in the candidate record and the
  build asserts the band [2014, 2018].
- **Coordinates arrive in three mixed encodings.** RODS packs lat/long in ddmm.ff,
  x10^3 and x10^6; the build decodes with a measured rule (0 ambiguous, 0 undecodable
  over a 3,088-value window) and asserts the Canadian envelope. An earlier single-scale
  assumption misread four good values as "corrupt" - the correction is recorded in
  `measurements.json`.
- **Publisher cross-check.** The panel's 2025 accident/incident counts reconcile with
  TSB's own annual summary to within 0.3%, and the label agrees with the publisher's
  separate INJURIES child table on all 1,624 positive rows.
- **Vintage.** Built from the RODS republish of 2026-10-21 (data through 2026-08-31).
  RODS is republished monthly; a later vintage is a new artifact and must be
  re-qualified before any number from it is quoted.

## Licence

Source: Open Government Licence - Canada 2.0 (attribution carries through to every
copy). Our derived compilation: CC0-1.0. Code and documentation: MIT. See `LICENSE.md`.
This is a derived research dataset, not a TSB official product.
