# SynTiSeD -- Synthetic Energy Datasets and NILM Experiments

<!-- This repository contains the datasets and experiment results accompanying the doctoral thesis:

> **Towards Realistic Synthetic Energy Datasets: Bridging the Gap between Simulation and Reality in Power Systems**
> by Michael Meiser, Saarland Informatics Campus, 2026 -->

## Overview

This repository provides synthetic and real energy datasets generated and evaluated using the **SynTiSeD** (Synthetic Time Series Data Generator) framework (https://github.com/mmeism/SynTiSeD_research). The datasets are designed for training and evaluating **Non-Intrusive Load Monitoring (NILM)** models and are stored in HDF5 format, compatible with [NILMTK](https://github.com/nilmtk/nilmtk-contrib).

The research demonstrates that NILM models trained on synthetic data can match or outperform models trained on real data in terms of transferability, and introduces the concept of **Unassigned Power Consumption (UPC)** as a critical component for generating realistic synthetic energy data.

All real-world data originates from the publicly available [GeLaP dataset](https://doi.org/10.1007/978-981-16-2377-6_5).

## Repository Structure

Each folder contains a dataset in `.h5` format, a visualization plot (`.png`), and NILM experiment results (`.xlsx`) for the electric kettle (single pattern type appliance) and washing machine (programmatic pattern type appliance).

```
.
├── real/                # Real-world data from the GeLaP dataset
├── noUPC/               # Synthetic data without UPC (Chapter 4)
├── ndUPC/               # Synthetic data with normally distributed noise as UPC (Chapter 4)
├── mUPC/                # Synthetic data with mapped (day-aligned) real UPC (Chapter 4)
├── rUPC/                # Synthetic data with randomly assigned real UPC (Chapter 4)
├── noSPCP/              # Synthetic data with real PCPs, no synthetic patterns (Chapter 5)
├── SPCP/                # Synthetic data with VA-generated synthetic PCPs (Chapter 5)
├── augmented/           # Combined noSPCP + SPCP dataset (Chapter 5)
├── r+r/                 # Doubled real data baseline (Chapter 5)
└── test/                # Test dataset (real data from unseen households)
```

### File Contents per Folder

| File | Description |
|------|-------------|
| `*_dataset.h5` | Energy consumption dataset in HDF5 format (NILMTK-compatible, 1 Hz resolution) |
| `*_kettle_exp.xlsx` | NILM experiment results for the electric kettle (single pattern type) |
| `*_wm_exp.xlsx` | NILM experiment results for the washing machine (programmatic pattern type) |
| `*_plot.png` | Visualization of the dataset |

## Datasets

### Chapter 4 -- Aggregated Power Consumption Data

These datasets investigate the impact of **Unassigned Power Consumption (UPC)** on the transferability of NILM models. UPC represents the power consumption of appliances for which no ground truth data is available -- a realistic scenario in real-world smart meter data.

| Dataset | Description |
|---------|-------------|
| **real** | Real-world data from the GeLaP dataset (5 synthesized households: hh01, hh04, hh07, hh14, hh15) |
| **noUPC** | Synthetic baseline without any form of UPC. Appliance PCPs are derived from real data. |
| **ndUPC** | Extends noUPC with normally distributed synthetic noise as UPC, mapped by day. |
| **mUPC** | Extends noUPC with real UPC (extracted from GeLaP), mapped to the corresponding day. |
| **rUPC** | Extends noUPC with real UPC, randomly assigned across days to decouple temporal dependencies. |

### Chapter 5 -- Appliance-Level Data with Virtual Appliances

These datasets evaluate whether **Virtual Appliances (VAs)** -- ML models that generate synthetic power consumption patterns (PCPs) -- can produce data suitable for training transferable NILM models.

| Dataset | Description |
|---------|-------------|
| **noSPCP** | Synthetic baseline using real PCPs from the GeLaP dataset (no synthetic power consumption patterns). |
| **SPCP** | Uses VA-generated synthetic PCPs (via the *lGen* model) instead of real appliance patterns. |
| **augmented** | Combination of noSPCP and SPCP, providing twice the training data with both real and synthetic PCPs. |
| **r+r** | Doubled real data baseline (same real data repeated) for comparison with the augmented dataset. |

### Test Data

| Dataset | Description |
|---------|-------------|
| **test** | Real data from unseen GeLaP households used for testing NILM transferability. Kettle: hh08, hh11, hh17. Washing machine: hh03, hh05, hh06, hh09, hh13, hh19, hh20. |

## Evaluation Methodology

All NILM experiments follow a rigorous evaluation methodology introduced in the thesis:

- **100 experiment repetitions** per configuration (based on Cochran's sample size formula at 95% confidence level)
- **5 NILM algorithms**: Sequence-to-Point (S2P), Sequence-to-Sequence (S2S), Denoising Autoencoder (DAE), Recurrent Neural Network (RNN), Window Gated Recurrent Units (wGRU)
- **MAEUH metric**: Mean Absolute Error on Unseen Houses -- measures transferability by averaging over 100 repetitions per unseen test household
- **Statistical significance testing**: Mann-Whitney-U test to determine whether differences between datasets are statistically significant
- All models trained using the [NILMTK-contrib](https://github.com/nilmtk/nilmtk-contrib) framework

## Key Findings

- **UPC is essential**: Synthetic data generated with UPC significantly improves the transferability of NILM models compared to data without UPC.
- **Synthetic data can outperform real data**: NILM models trained on synthetic data with UPC (especially rUPC) achieve comparable or significantly better results than models trained on real data.
- **Virtual Appliances work**: Synthetic PCPs generated by VAs produce NILM models that outperform models trained on real data and on synthetic data using real PCPs.
- **More varied data helps**: The augmented dataset (combining real and synthetic PCPs) consistently provides the best results across most NILM algorithms.
- **Reliable evaluation matters**: Single experiment results are insufficient due to the stochastic nature of neural networks; repeated experiments with statistical testing are necessary.

## Data Format

All datasets are stored in **HDF5 format** compatible with NILMTK. The data is sampled at **1 Hz** resolution and contains:

- Aggregated smart meter data (total household power consumption)
- Appliance-level ground truth data (individual appliance power consumption)
- Multiple simulated households with 14 days of data each

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
