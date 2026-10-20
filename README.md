# rePredRet Models Data Repository

This repository contains pre-built retention time prediction models for the [rePredRet](https://github.com/stanstrup-metabolomics/rePredRet) package.

## Overview

**rePredRet** predicts retention times (RTs) by directly mapping between chromatographic systems using monotonically constrained GAMs. This repository contains ~50,000 models between the reversed-phase (RP) systems of the [RepoRT](https://github.com/michaelwitting/RepoRT) database that share at least 10 compounds.

The models are built and updated automatically by the [Build and Update Models](https://github.com/stanstrup-metabolomics/rePredRet/actions/workflows/build-models.yml) workflow in the rePredRet repository. Do not edit model files by hand.

## Repository Structure

```
rePredRet-models/
├── models/                       # One directory per model
│   ├── 0001_to_0002/             # Model from system 0001 (source) to 0002 (target)
│   │   ├── model.json            # Prediction grid, calibration data and statistics
│   │   ├── model.rds             # R model object (Git LFS)
│   │   ├── ci_grid.csv           # Prediction grid
│   │   └── calibration_data.csv  # Calibration compounds
│   └── ...
├── model_index.csv               # One row per model with statistics
├── report_version.txt            # RepoRT commit the models were built from
├── build_cache.rds               # Build state for incremental updates (Git LFS)
└── README.md
```

## Model Files

- **model.json**: `sys1_id`, `sys2_id`, `n_compounds`, `method`, `predictions` (rows with `x`, `pred`, `lower`, `upper`: predicted target RT with 95% prediction interval on a grid of 1000 source RTs), `calibration` (rows with `x`, `y`, `compound`) and `stats` (`median_ci_width`, `median_error`, `build_time`). Used by the web viewer and `rePredRet_model()`.
- **model.rds**: the model object from `rePredRet::build_model()`. Stored in Git LFS.
- **ci_grid.csv**: the prediction grid as CSV (`rt_source`, `rt_target_predicted`, `ci_lower`, `ci_upper`).
- **calibration_data.csv**: the calibration compounds (`rt_source`, `rt_target`, `compound_id`: InChI without stereochemistry).

## Model Index

`model_index.csv` has one row per model:

| Column | Description |
|--------|-------------|
| `sys1_id` | Source system ID |
| `sys2_id` | Target system ID |
| `n_compounds` | Number of calibration compounds |
| `median_ci_width` | Median width of the 95% prediction interval (minutes) |
| `median_error` | Median absolute error at the calibration compounds (minutes) |

## Usage

### From R

```r
remotes::install_github("stanstrup-metabolomics/rePredRet")
library(rePredRet)

models <- rePredRet_models()              # the model index
model <- rePredRet_model("0001", "0002")  # model.json contents
plot_model("0001", "0002")
```

Files are fetched on demand and cached locally.

### Web Viewer

The [model viewer](https://stanstrup-metabolomics.github.io/rePredRet/model_viewer.html) fetches models from this repository on demand.

### Direct Download

```bash
# Model index and a model
curl -O https://raw.githubusercontent.com/stanstrup-metabolomics/rePredRet-models/main/model_index.csv
curl -O https://raw.githubusercontent.com/stanstrup-metabolomics/rePredRet-models/main/models/0001_to_0002/model.json

# LFS files (.rds) must be downloaded from media.githubusercontent.com;
# raw.githubusercontent.com only returns the LFS pointer
curl -O https://media.githubusercontent.com/media/stanstrup-metabolomics/rePredRet-models/main/models/0001_to_0002/model.rds
```

## Model Building

The workflow checks RepoRT daily and rebuilds when its master branch has changed:

1. Datasets are compared to `build_cache.rds` by MD5 hash of their RT data
2. Only models involving new or changed datasets (or with missing files) are rebuilt; models for removed datasets are deleted
3. The changed model directories are committed and pushed, followed by `model_index.csv`, `build_cache.rds` and `report_version.txt`

## Data Source

Models are built from the [RepoRT](https://github.com/michaelwitting/RepoRT) database of community-contributed LC-MS retention times. `report_version.txt` contains the RepoRT commit used.

## Citation

If you use these models in your research, please cite:

> Stanstrup, J. et al. (2015). PredRet: Prediction of Retention Time by Direct Mapping between Multiple Chromatographic Systems. *Analytical Chemistry*, 87(18), 9421-9428.

And the RepoRT database:

> Kretschmer, F. et al. (2024). RepoRT: a comprehensive repository for small molecule retention times. *Nature Methods*, 21, 153-155.

## License

Model data: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

Code (rePredRet package): GPL-3

## Links

- **Main package:** https://github.com/stanstrup-metabolomics/rePredRet
- **Website:** https://stanstrup-metabolomics.github.io/rePredRet/
- **RepoRT database:** https://github.com/michaelwitting/RepoRT
- **Issues:** https://github.com/stanstrup-metabolomics/rePredRet/issues
