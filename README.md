# SynTiSeD Repository Overview

## Core Purpose
This repository provides synthetic and real energy datasets for energy-domain machine learning, built on the [SynTiSeD](https://github.com/mmeism/SynTiSeD_research) (Synthetic Time Series Data Generator) framework and compatible with [NILMTK](https://github.com/nilmtk/nilmtk-contrib). The datasets are evaluated on **Non-Intrusive Load Monitoring (NILM)** and **short-term load forecasting**, two downstream tasks which probe complementary properties of the data: structural/local accuracy at high resolution (NILM) and long-range behavior such as daily seasonality (forecasting).

## Key Datasets
The repository organizes datasets across two research chapters:

**Chapter 4** examines Unassigned Power Consumption (UPC) impact through variants: real data, noUPC, ndUPC, mUPC,
and rUPC configurations.

**Chapter 5** evaluates Virtual Appliances generating synthetic patterns across noSPCP, SPCP, augmented, and r+r
datasets, plus a held-out test set.

## Downstream Tasks
The utility of the synthetic data is assessed on two structurally different downstream tasks:

- **NILM** (disaggregation) — probes structural / local accuracy at high resolution. Results in `experiments/nilm/`.
- **Short-term load forecasting** (day-ahead) — probes long-range / seasonal behavior. Results in `experiments/forecasting/`.

Both tasks use the same transfer protocol: train a model on a given dataset, then evaluate on the unseen GeLaP test households.

## Repository Structure
```
SynTiSeD_data/
├── datasets/                 # one folder per dataset variant (data + visualization)
│   ├── real/  noUPC/  ndUPC/  mUPC/  rUPC/  noSPCP/  SPCP/  augmented/  r+r/  test/
│   │     └── <ds>_dataset.h5.gz,  <ds>_plot.png
└── experiments/
    ├── nilm/                 # NILM results + README
    │     └── <ds>_kettle_exp.xlsx,  <ds>_wm_exp.xlsx
    └── forecasting/          # forecasting results + README
          └── forecasting_DLinear.xlsx,  forecasting_NBEATS.xlsx
```

`<ds>` ∈ {real, noUPC, ndUPC, mUPC, rUPC, noSPCP, SPCP, augmented, r+r, test}.

## Main Claims About Model Performance
Across both tasks, models trained on the realistic synthetic datasets achieve transferability **comparable to and in some configurations slightly better than** models trained on real data, evaluated with effect sizes and confidence intervals at the household level. 

## Data Specifications
- Format: HDF5 (NILMTK-compatible)
- Resolution: 1 Hz sampling
- Content: Aggregated household consumption plus appliance-level ground truth
- Scale: Multiple households with 14 days per household (28 days for augmented and r+r)“
- Large data files (`*.h5.gz`) are tracked with Git LFS.

## Real Data Source
All real-world data originates from the publicly available [GeLaP dataset](https://doi.org/10.1007/978-981-16-2377-6_5), with test households drawn from unseen GeLaP homes.

## Evaluation Approach
Both tasks treat the **household** as the unit of analysis and compare datasets using **effect sizes and 95 % confidence intervals**.

- **NILM:** 100 repetitions per configuration across 5 NILM algorithms (S2P, S2S, DAE, RNN, wGRU).
- **Forecasting:** two forecasting models (DLinear, N-BEATS) and two scaled error measures (MASE, RMSSE), with 10 seeds per household.

## Citation
If you use these datasets in your research, please cite:

<!-- ```bibtex
@phdthesis{meiser2024syntised,
  title     = {Towards Realistic Synthetic Energy Datasets: Bridging the Gap between Simulation and Reality in Power Systems},
  author    = {Meiser, Michael},
  year      = {2024},
  school    = {Saarland University},
  type      = {Doctoral Thesis}
}
``` -->

```bibtex
@article{meiser2024generation,
  title={Generation of meaningful synthetic sensor data—Evaluated with a reliable transferability methodology},
  author={Meiser, Michael and Duppe, Benjamin and Zinnikus, Ingo},
  journal={Energy and AI},
  volume={15},
  pages={100308},
  year={2024},
  publisher={Elsevier}
}
```

## License
Please refer to the thesis and the original [GeLaP dataset](https://doi.org/10.1007/978-981-16-2377-6_5) for licensing information regarding the real-world data.

## Acknowledgements
This work was conducted at the German Research Center for Artificial Intelligence (DFKI) and Saarland University, Saarland Informatics Campus.
