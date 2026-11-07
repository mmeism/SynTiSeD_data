# Load-Forecasting Transferability Evaluation

This folder contains the results of the **load-forecasting** evaluation — a second, complementary downstream task to NILM. While NILM probes the structural and local accuracy of the synthetic data, forecasting probes long-range properties such as daily seasonality. 
The experiment uses the following datasets of this repository (`real`, `noUPC`, `ndUPC`, `mUPC`, `rUPC`, `noSPCP`, `SPCP`, `augmented`, `r+r`, `test`).

## Setup

- **Task:** day-ahead forecasting — predict the next 96 steps from a 2-day context (192 steps).
- **Signal:** household aggregate = sum of the three mains phases (`meter1+2+3`) of each `*_dataset.h5`,
  resampled to a 15-minute resolution (German smart-meter resolution).
- **Transfer protocol:** train the forecasting model on a **synthetic** dataset, evaluate on the **real, unseen** households of the `test` set (all 10 test households, which include the NILM test households of the kettle and the washing machine). This mirrors the NILM transferability setup.
- **Rolling-origin evaluation:** each test household is forecast at every non-overlapping day-ahead origin after the first 192 steps (one forecast per day), so that each per-household value aggregates all test days rather than a single day.
- **Models:** DLinear (primary, seed-stable linear baseline) and N-BEATS (a structurally different, nonlinear deep model) — to check that results are not an artefact of a single model.
- **Metrics:** both scaled by the seasonal-naive forecast (m = 96) over the complete test series of the household, so that values < 1 beat the seasonal-naive benchmark and are comparable across households:
  - **MASE** (Mean Absolute Scaled Error) — absolute-error based.
  - **RMSSE** (Root Mean Squared Scaled Error) — squared-error based; penalises missed peaks more and is less favourable to damped forecasts.
- **Repetitions:** 10 random seeds per household (Monte-Carlo control for training stochasticity).
- **Statistics:** the **household** is the unit of analysis. We report per-dataset effect sizes (mean relative change in error vs. real training) with **95 % confidence intervals** (Student-t over the 10 households), not p-values. This is consistent with the estimation-based methodology used for the NILM evaluation.

## Results (summary)

Across both models and both error measures, training on the synthetic datasets performs **comparably** to training on real data, with a model-dependent spread in both directions:

- **DLinear:** all datasets within about ±1 % of real, except the two ablations: `noUPC` is clearly worse on RMSSE (−2.7 %) and `ndUPC` clearly better on MASE (+4.3 %). Owing to the narrow confidence intervals, several differences below 1 % are formally clear but practically negligible.
- **N-BEATS** (more discriminating): `mUPC`, `rUPC`, `SPCP`, `augmented` and `r+r` comparable; the degraded no UPC baseline (`noUPC`) is clearly worse than real on both metrics (MASE −8.0 %, RMSSE −4.4 %), `noSPCP` slightly worse on MASE (−4.0 %; on RMSSE, its confidence interval includes zero), and `ndUPC` clearly better on both metrics (MASE +7.0 %, RMSSE +3.0 %).

## Negative controls

To verify that the evaluation detects training data without realistic temporal structure, both models are additionally trained on three destroyed versions of the real training data (5 random draws each, 10 seeds per draw):

- **shuffle:** all values of a household permuted (no temporal structure),
- **dayshuffle:** order of the days permuted (daily structure preserved),
- **dayrotate:** each day rotated by a random offset (daily periodicity destroyed).

Mean relative change of the error against real training (here **positive = worse**), and number of draws whose confidence interval excludes zero:

| | shuffle | dayshuffle | dayrotate |
|---|---|---|---|
| DLinear MASE | +13.3 % (5/5) | +1.4 % (4/5) | +6.0 % (3/5) |
| DLinear RMSSE | +6.2 % (5/5) | −0.3 % (0/5) | +4.2 % (4/5) |
| N-BEATS MASE | +4.3 % (0/5) | +2.8 % (0/5) | +13.2 % (5/5) |
| N-BEATS RMSSE | −0.1 % (0/5) | +0.9 % (0/5) | +7.4 % (5/5) |

DLinear detects the removal of all temporal structure, N-BEATS the destruction of the daily periodicity; reordering whole days, which preserves the daily structure, has little effect, as expected.

The forecasting result therefore extends the viability of the synthetic data beyond NILM to a second, structurally different downstream task.

## Files: `forecasting_DLinear.xlsx` and `forecasting_NBEATS.xlsx`

The results are split by model — one self-contained workbook per forecasting model. Each has the same sheet
layout (`<model>` ∈ {`DLinear`, `NBEATS`}):

| Sheet | Content |
|-------|---------|
| `Summary` | One row per dataset: mean/median MASE and RMSSE for this model. |
| `<dataset>_<model>` | Per-dataset config header + per-household MASE/RMSSE (mean, std over seeds). 9 sheets (one per dataset). |
| `descriptive` | Per (dataset, metric): mean, relative change vs. real [%], 95 % CI, households better/N. |
| `per_seed_<model>` | Full transparency: per household × seed0…seed9 (+ mean/std), MASE and RMSSE blocks per dataset. |
