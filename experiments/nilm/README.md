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

with `<ds>` ∈ {real, noUPC, ndUPC, mUPC, rUPC, noSPCP, SPCP, augmented, r+r}. Each file holds the per-household per-repetition errors and the aggregated comparison against real training.

## Result (summary)

Across appliances and architectures, NILM models trained on the realistic synthetic datasets reach transferability **comparable to and in some configurations slightly better than** models trained on real data. 