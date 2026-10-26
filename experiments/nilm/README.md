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

with `<ds>` ∈ {real, noUPC, ndUPC, mUPC, mUPC_run2, mUPC_run4, mUPC_run5, rUPC, noSPCP, SPCP, augmented, r+r}. Each file holds the configuration header and, per test household and algorithm, the MAE of the 100 repetitions with summary statistics (mean, max, min, standard deviation), as well as the MAE averaged over the test households (MAEUH). The comparisons against real training (effect sizes with 95 % confidence intervals) are reported in the thesis.

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

## Result (summary)

Across appliances and architectures, NILM models trained on the realistic synthetic datasets reach transferability **comparable to and in some configurations slightly better than** models trained on real data. 