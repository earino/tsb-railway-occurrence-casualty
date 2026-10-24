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

# TSB Railway Occurrence Casualty (Canada)

A binary prediction task built from the Transportation Safety Board of Canada's Rail
Occurrence Database System (RODS): **when a railway occurrence begins on the Canadian
federal network, will it result in at least one casualty (a fatality or any injury)?**

A railway occurrence begins at a known place, time and operating context - a crossing
accident, a derailment, a trespasser event, a fire, a collision, a movement exceeding
limits of authority. At that instant, the only things known are pre-onset context:
where (subdivision, nearest location, province, region, coordinates), which railway,
when (date, local hour, season) and what the train was doing (activity type). The
casualty outcome unfolds after onset. Every feature in this dataset is a property of the
occurrence at onset; everything TSB records during or after the occurrence - its own
accident/incident type taxonomy (which embeds outcomes), injury counts and flags, damage
and dangerous-goods fields, weather taken at the scene, investigation and process dates
and the free-text summary - is excluded by construction and never shipped.

- **Grain:** one row per TSB rail occurrence (occurrence number `occ_no`).
- **Panel:** 2016-01-01 .. 2026-08-31, from the monthly-republished RODS vintage of
  2026-10-21 (the publisher re-releases on or soon after the 15th of each month).
  The panel start is measured, not assumed: TSB's pre-2016 rows carry a legacy
  taxonomy-era signature (HISTORICAL / DG LEAKER categories) that fades to zero only
  from 2016, so earlier years describe a different reporting regime.
- **Splits (temporal, by occurrence date):** train 2016-01-01..2021-12-31 (7,925 rows,
  883 positives, 11.14%); eval 2022-01-01..2024-12-31 (3,831 rows, 463 positives,
  12.09%); holdout 2025-01-01..2026-08-31 (1,976 rows, 278 positives, 14.07%). Both
  classes appear in every split and every year; each positive is an independent
  occurrence.
- **Label:** `casualty` = 1 iff the occurrence had at least one fatality or injury,
  derived from the publisher's injury indicator and totals (which are excluded from
  every shipped file). Label integrity was cross-checked against the publisher's
  separate INJURIES child table on all 1,624 positive panel rows, and panel counts
  reconcile with TSB's own 2025 annual summary to within 0.3% (832 vs 831 accidents,
  331 vs 332 incidents).
- **Baseline (runner contract, unmodified):** eval AUC 0.8126, from the benchmark's own
  `train.py` and reproduced exactly by `validate.py`'s independent recompute.
  Construction-time references the model had to beat: trailing-12-month casualty-rate
  persistence 0.527 eval / 0.495 holdout; best single context column (activity_type)
  0.707 / 0.699. The task has real headroom over both - it is not a persistence
  exercise. See `VERIFICATION.md`.

The dataset is **derived**: it is not a TSB official product, and the TSB has not
reviewed or endorsed it. The source data are licensed under the
[Open Government Licence - Canada 2.0](https://open.canada.ca/en/open-government-licence-canada);
see `LICENSE.md` for the terms that carry through to every copy, and
`DATA_DICTIONARY.md` for every column's documented meaning, unit and reference frame.

## Layout

```
public/train.csv      train split (2016..2021)
public/eval.csv       eval split (2022..2024)
private/holdout.csv   holdout split (2025..2026-08) - ships like the other splits and
                      is never copied into a runner workdir
meta.json             task metadata the runner reads
quality.json          the qualification descriptor (splits, timing, units, exclusions)
```

## Use

```bash
python3 get_dataset.py --dest ./task     # download + verify + restore the layout
python3 code/qualify_dataset.py ./task   # re-run the qualification gate on the bytes
sh baseline/reproduce_baseline.sh ./task # reproduce the recorded baseline
```

See `REPRODUCE.md` for the full reproduction chain (source fetch -> build -> gate ->
baseline) and `VERIFICATION.md` for what was verified, how, and what is not covered.
