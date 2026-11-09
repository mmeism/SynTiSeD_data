# NILM Transferability Evaluation

This folder contains the **Non-Intrusive Load Monitoring (NILM)** evaluation results — the first of the two downstream tasks (see the root `README.md`). NILM disaggregates the household aggregate into individual appliance consumption and probes the *structural* and *local* accuracy of the synthetic data at high
resolution.

## Setup

- **Transfer protocol:** train NILM models on a dataset (real or synthetic), then evaluate on **real, unseen** households of the `test` set (`datasets/test/`). This measures how well a model trained on one dataset transfers to real data.
- **Appliances:**
  - **kettle** 
  - **washing machine (wm)** 
- **Models:** 5 NILM algorithms — S2P (seq2point), S2S (seq2seq), DAE, RNN, and wGRU (WindowGRU).
- **Statistics:** because NILM training is stochastic, each configuration is run with **100 repetitions**. We report **effect sizes with 95 % confidence intervals** (estimation-based, no p-values), consistent across the thesis.

## Files

One spreadsheet per (dataset, appliance):

```
<ds>_kettle_exp.xlsx      # kettle results for dataset <ds>
<ds>_wm_exp.xlsx          # washing-machine results for dataset <ds>
```

with `<ds>` ∈ {real, noUPC, ndUPC, mUPC, mUPC_run2, mUPC_run4, mUPC_run5, rUPC, fUPCk, fUPCs, noSPCP, SPCP, augmented, r+r} (fUPCk and fUPCs: kettle only). Each file holds the configuration header and, per test household and algorithm, the MAE of the 100 repetitions with summary statistics (mean, max, min, standard deviation), as well as the MAE averaged over the test households (MAEUH). The comparisons against real training (effect sizes with 95 % confidence intervals) are reported in the thesis.

## Simulation runs

To assess the variation between simulation runs, the mUPC configuration was simulated five times. The runs use the same action sequences and UPC and differ only in the randomly selected PCPs:

| Run | Dataset |
|---|---|
| 1 | `mUPC` |
| 2 | `mUPC_run2` |
| 3 | `noSPCP` (also used in Chapter 5) |
| 4 | `mUPC_run4` |
| 5 | `mUPC_run5` |

The time index of each run is shifted so that the runs do not overlap; the simulated days correspond to the same 14 real days. For the kettle, S2S, DAE and wGRU (run 4) and S2S and DAE (run 5) were not computed owing to the computational cost; these rows are marked "not computed". The results are summarized in Appendix C of the thesis.

## Control experiment with filtered UPC

To examine whether the advantage of the mUPC dataset for the kettle stems from residuals of the UPC computation, two filtered variants of the mUPC dataset were created. Only the UPC (`meter3`) is changed; action sequences, appliance patterns and labels are identical to `mUPC`.

| Dataset | UPC |
|---|---|
| `fUPCk` | In every interval in which the real kettle of the training household or the kettle label is active (> 100 W, extended by ±15 s), the UPC is replaced by a straight line between the medians of the 60 s before and after the interval. This removes residuals of the kettle that are aligned with its activations (0.5–1.4 % of the time). |
| `fUPCs` | The UPC is replaced by its moving median over 601 s (centred). This removes all short transients, including kettle-like ones at arbitrary times (3–9 % of the UPC energy); the base load is retained. |

The NILM models were trained on the kettle training households (hh01, hh04, hh07, hh14) and tested on the real test households hh08, hh11 and hh17, as for the other datasets. S2P, S2S and DAE were run with 100 repetitions. The files `fUPCk_kettle_exp.xlsx` and `fUPCs_kettle_exp.xlsx` have the same layout as the other kettle files.

**Result:** removing the aligned kettle residuals (`fUPCk`) retains both the improvement over `noUPC` and the advantage over `real`; smoothing out all short transients (`fUPCs`) retains most of the improvement over `noUPC`, but the advantage over `real` is lost. The advantage over real data therefore does not stem from residuals of the kettle at its activation times, but depends on short transients in the UPC. The results are summarized in the appendix of the thesis.

## Result (summary)

Across appliances and architectures, NILM models trained on the realistic synthetic datasets reach transferability **comparable to and in some configurations slightly better than** models trained on real data. 