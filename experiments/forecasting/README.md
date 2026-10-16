# Load-Forecasting Transferability Evaluation

This folder contains the results of the **load-forecasting** evaluation — a second, complementary downstream task to NILM. While NILM probes the structural and local accuracy of the synthetic data, forecasting probes long-range properties such as daily seasonality. The experiment uses the **same datasets** as the rest of this repository (`real`, `noUPC`, `ndUPC`, `mUPC`, `rUPC`, `noSPCP`, `SPCP`, `augmented`, `r+r`, `test`).

## Setup

- **Task:** day-ahead forecasting — predict the next 96 steps from a 2-day context (192 steps).
- **Signal:** household aggregate = sum of the three mains phases (`meter1+2+3`) of each `*_dataset.h5`,
  resampled to a 15-minute resolution (German smart-meter resolution).
- **Transfer protocol:** train the forecasting model on a **synthetic** dataset, evaluate on the **real,unseen** households of  the `test` set (the same households used as the NILM test set). This mirrors the NILM transferability setup.
- **Models:** DLinear (primary, seed-stable linear baseline) and N-BEATS (a structurally different, nonlinear deep model) — to check that results are not an artefact of a single model.
- **Metrics:** both scaled by the in-sample seasonal-naive forecast (m = 96), so that values < 1 beat the seasonal-naive benchmark and are comparable across households:
  - **MASE** (Mean Absolute Scaled Error) — absolute-error based.
  - **RMSSE** (Root Relative Squared Scaled Error) — squared-error based; penalises missed peaks more and does not reward damped forecasts.
- **Repetitions:** 10 random seeds per household (Monte-Carlo control for training stochasticity).
- **Statistics:** the **household** is the unit of analysis. We report per-dataset effect sizes (mean relative change in error vs. real training) with **95 % confidence intervals** (Student-t over the 10 households), not p-values. This is consistent with the estimation-based methodology used for the NILM evaluation.

## Results (summary)

Across both models and both error measures, training on the synthetic datasets performs **comparably** to training on real data (relative differences of a few percent; confidence intervals include zero), with a model-dependent spread in both directions:

- **DLinear:** all datasets comparable to real.
- **N-BEATS** (more discriminating): `mUPC`, `rUPC`, `augmented`, `r+r` and `ndUPC` comparable (some nominally better); the degraded no UPC baseline (`noUPC`) and `SPCP` are worse than real.
- For individual models (notably WindowGRU on the washing-machine aggregate in NILM, and ndUPC in forecasting) synthetic training is slightly better than real.

The forecasting result therefore extends the viability of the synthetic data beyond NILM to a second, structurally different downstream task.

## Files: `forecasting_DLinear.xlsx` and `forecasting_NBEATS.xlsx`

The results are split by model — one self-contained workbook per forecasting model. Each has the same sheet
layout (`<model>` ∈ {`DLinear`, `NBEATS`}):

| Sheet | Content |
|-------|---------|
| `Summary` | One row per dataset: mean/median MASE and RMSSE for this model. |
| `<dataset>_<model>` | Per-dataset config header + per-household MASE/RMSSE (mean, std over seeds). 8 sheets (one per dataset). |
| `descriptive` | Per (dataset, metric): mean, relative change vs. real [%], 95 % CI, households better/N. |
| `per_seed_<model>` | Full transparency: per household × seed0…seed9 (+ mean/std), MASE and RMSSE blocks per dataset. |

s
