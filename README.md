# rePredRet Models Data Repository

This repository contains pre-computed retention time prediction models for the [rePredRet](https://github.com/stanstrup/rePredRet) package.

## Overview

**rePredRet** predicts retention times (RTs) by directly mapping between chromatographic systems using monotonically constrained GAMs. This data repository contains ~50,000 pre-computed prediction models between LC-MS systems from the [RepoRT](https://github.com/michaelwitting/RepoRT) database.

## Repository Structure

```
rePredRet-models/
├── models/                    # Model directories
│   ├── 0001_to_0002/         # Model from system 0001 to 0002
│   │   ├── model.json        # Model data for web viewer (30KB)
│   │   ├── model.rds         # R object for predictions (35KB)
│   │   └── calibration_data.csv  # Calibration points (8KB)
│   └── ... [~50,000 models]
├── build_cache.rds           # Build state tracking
├── model_index.csv           # Searchable model metadata
└── README.md                 # This file
```

## Model Files

Each model directory contains 3 files:

### model.json (~30KB)
JSON file with model data for the interactive web viewer:
- Model metadata (system IDs, compound count)
- Prediction curve (RT predictions with confidence intervals)
- Calibration data points
- Model statistics

### model.rds (~35KB)
R data file containing the fitted GAM model object:
- Used by `rePredRet` package for making predictions
- Contains full model specification
- Can be loaded with `readRDS()`

### calibration_data.csv (~8KB)
CSV file with calibration compound data:
- Columns: `rt_source`, `rt_target`, `compound_id`
- Used for transparency and verification
- Can be loaded with any CSV reader

## Usage

### From R

```r
# Install rePredRet package
remotes::install_github("stanstrup/rePredRet")

# Load a specific model
library(rePredRet)
model_path <- "https://raw.githubusercontent.com/stanstrup/rePredRet-models/main/models/0001_to_0002/model.rds"
model <- readRDS(url(model_path))

# Or use package functions
predictions <- rePredRet_predict(...)
```

### From Web Viewer

The [rePredRet website](https://stanstrup.github.io/rePredRet/) provides an interactive viewer that fetches models from this repository on-demand.

### Programmatic Access

Fetch model data via raw GitHub URLs:

```bash
# Get model JSON
curl https://raw.githubusercontent.com/stanstrup/rePredRet-models/main/models/0001_to_0002/model.json

# Get model index
curl https://raw.githubusercontent.com/stanstrup/rePredRet-models/main/model_index.csv
```

## Model Index

The `model_index.csv` file contains metadata for all models:
- `model_key`: Model identifier (e.g., "0001_to_0002")
- `from_id`: Source system ID
- `to_id`: Target system ID
- `method_type`: Chromatography method (RP, HILIC)
- `n_compounds`: Number of calibration compounds
- `median_error`: Median prediction error (seconds)
- `median_ci_width`: Median confidence interval width
- `build_time`: Model building time (seconds)

## Model Building

Models are automatically rebuilt when:
- New datasets added to RepoRT
- Existing dataset data changes
- Algorithm improvements

Rebuild process:
1. GitHub Actions workflow detects RepoRT updates
2. Computes MD5 hashes of dataset RT data
3. Rebuilds only models with changed source data
4. Updates this repository via sparse checkout

See [build_cache.rds](./build_cache.rds) for current build state.

## Data Source

Models are built from the [RepoRT](https://github.com/michaelwitting/RepoRT) database:
- Community-contributed LC-MS retention time data
- Standardized format with InChI identifiers
- Multiple chromatographic methods (RP, HILIC)

## Citation

If you use these models in your research, please cite:

```
Stanstrup et al. (2015) PredRet: Prediction of retention time by direct
mapping between different chromatographic systems. Analytical Chemistry.
```

And the RepoRT database:

```
Witting et al. (2023) RepoRT: a comprehensive repository for
liquid chromatography retention times. Nature Methods.
```

## License

Model data: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

Code (rePredRet package): GPL-3

## Links

- **Main package:** https://github.com/stanstrup/rePredRet
- **Website:** https://stanstrup.github.io/rePredRet/
- **RepoRT database:** https://github.com/michaelwitting/RepoRT
- **Issues:** https://github.com/stanstrup/rePredRet/issues

## Statistics

- **Total models:** ~50,000
- **Repository size:** ~3.7 GB
- **Individual model size:** ~73 KB
- **Last updated:** 2026-01-18
- **RepoRT version:** master
