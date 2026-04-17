# BioScape Analysis — *Leucadendron argenteum* Detection & Mapping
### Airborne Hyperspectral Imaging · LVIS LiDAR · Table Mountain National Park

[![DOI](https://zenodo.org/badge/1151417486.svg)](https://doi.org/10.5281/zenodo.19233253)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![NASA BioSCape](https://img.shields.io/badge/Data-NASA%20BioSCape%202023-blue)](https://www.earthdata.nasa.gov/data/projects/bioscape)
[![AVIRIS-NG](https://img.shields.io/badge/Sensor-AVIRIS--NG%20L3-orange)](https://aviris.jpl.nasa.gov/)
[![LVIS](https://img.shields.io/badge/Sensor-LVIS%20L2-orange)](https://lvis.gsfc.nasa.gov/)

---

## Overview

This repository contains the full analytical pipeline developed for the study:

> **Rutlokoane, P., O'Farrell, P., & Blanchard, R.** — *Monitoring Leucadendron argenteum populations: exploring opportunities offered by emerging technologies.* JGR Biogeosciences (under review).

The Silvertree (*Leucadendron argenteum*) is a rare, silver-leaved endemic tree restricted to the Cape Peninsula of South Africa and listed as Vulnerable under the SANBI Red List. This study evaluates whether NASA's AVIRIS-NG hyperspectral imaging and LVIS full-waveform LiDAR, collected during the 2023 BioSCape campaign, can reliably detect, distinguish, and map *L. argenteum* populations across Table Mountain National Park.

The pipeline produces:
- A validated spectral library of 26 fynbos vegetation species (1,229 spectra)
- LiDAR canopy height profiles and field validation for 7 study plots (35 trees)
- A species classification model (XGBoost F1 = 0.631) with SHAP wavelength importance
- A landscape-scale probability map of *L. argenteum* occurrence across the Cape Peninsula

---

## Repository Structure

```
BioScape-Analysis/
│
├── 📁 Spectral_Library/
│   ├── Phemelo-R_Spectral_Extraction.ipynb   ← START HERE (Step 1)
│   └── Data/
│       └── Species_data_final4.csv            ← Input: field species coordinates
│
├── 📁 LiDAR_Analysis/
│   ├── Phemelo-R_LiDAR_Analysis.ipynb         ← Step 2
│   └── Data/
│       ├── site_polygons/                      ← Input: 7 plot shapefiles
│       └── Site RH.xlsx                        ← Input: field-measured tree heights
│
├── 📁 Hyperspectral_Analysis/
│   ├── Phemelo-R_Hyperspectral_Analysis.ipynb ← Step 3
│   └── Data/
│       ├── Species_data_final4.csv/                    ← Input: field species coordinates
│       ├── species_spectra4.csv/                       ← Input: field species spectra
│       └── Phemelo_TMNP.shp/                           ← Input: Table Mountain National Park boundary
│
└── README.md
```

> **Note on input data:** Only the data required at the start of each notebook is included in this repository. All intermediate outputs (spectral library rds, canopy height rasters, probability maps) are generated automatically by running the notebooks in order.

---

## Getting Started

### Prerequisites

**Step 1 (Spectral Extraction)** runs on an **R kernel** in Jupyter Notebook. All other steps run on a standard **Python 3.12** kernel.

**R packages:**
```r
install.packages(c("terra", "sf", "dplyr", "ncdf4", "ggplot2"))
```

**Python packages:**
```bash
pip install numpy pandas matplotlib seaborn scikit-learn xgboost shap rasterio pyproj netCDF4 earthaccess h5py pyreadr
```

To use an R kernel in Jupyter:
```r
install.packages("IRkernel")
IRkernel::installspec()
```

### Data Access

| Dataset | Source | Access |
|---|---|---|
| AVIRIS-NG L3 Hyperspectral | NASA JPL / BioSCape | [earthdata.nasa.gov](https://www.earthdata.nasa.gov/data/projects/bioscape/data-access-tools) |
| LVIS Level-2 LiDAR | NASA NSIDC DAAC | [LVIS L2 V001](https://search.earthdata.nasa.gov/search?q=LVIS&fpj=BioSCape) |

A free NASA Earthdata account is required to download both datasets.

---

## Running the Pipeline

The three notebooks must be run **in order**. Each notebook produces outputs that are required by the next.

---

### Step 1 — Spectral Library Extraction
📁 `Spectral_Library/Phemelo-R_Spectral_Extraction.ipynb`
**Kernel: R**

This notebook builds the species spectral library from the AVIRIS-NG Level-3 tiles. It takes the field-collected GPS coordinates of 26 vegetation species and extracts the corresponding hyperspectral reflectance values directly from the imagery, one tile at a time.

**What it does:**
1. Loads field species coordinates and reprojects them into the AVIRIS Albers coordinate system
2. Reads wavelength metadata from the AVIRIS NetCDF files (425 bands, 380–2510 nm)
3. Loops through every AVIRIS tile, finds which species points fall within it, and extracts the full spectrum at each point
4. Checks and corrects reflectance scaling (converts ×10,000 integers to 0–1 float)
5. Calculates NDVI for every extracted spectrum and removes non-vegetation points (NDVI < 0.2)
6. Removes spectra where more than 50% of bands are missing
7. Saves the cleaned spectral library as `.rds` and `.csv`

**Output:** `species_spectra4.csv` — used in Step 3

---

### Step 2 — LiDAR Canopy Height Analysis
📁 `LiDAR_Analysis/Phemelo-R_LiDAR_Analysis.ipynb`
**Kernel: Python**

This notebook processes the LVIS Level-2 full-waveform LiDAR data for the seven 10 m × 10 m field plots established on the slopes of Table Mountain. It extracts canopy height metrics and validates them against field-measured *L. argenteum* tree heights.

> ⚠️ The 100 LVIS granule files contain approximately 35 million shots and around 15–20 GB of data. The notebook loads and filters one file at a time to avoid memory issues.

**What it does:**
1. Loads the 7 plot boundary shapefiles and field height measurements
2. Downloads and filters LVIS granules (removes saturated shots, shots without valid ground elevation, and low-energy returns)
3. Calculates RH98 (the height at which cumulative waveform energy reaches 98%) for each retained shot
4. Clips shots to each plot boundary and computes median and maximum RH98 per plot
5. Plots waveform vertical structure profiles for all 7 sites (Figure 3 in the paper)
6. Rank-pairs LVIS shots with field trees and runs linear regression (Figure 4 in the paper)
7. Reports R², RMSE, and p-values for individual and plot-level comparisons

**Output:** Summary statistics and figures — the LiDAR canopy height raster used in Step 3 is generated separately (see notebook instructions).

---

### Step 3 — Hyperspectral Classification & Probability Mapping
📁 `Hyperspectral_Analysis/Phemelo-R_Hyperspectral_Analysis.ipynb`
**Kernel: Python**

This is the main classification pipeline. It takes the validated spectral library from Step 1, trains and evaluates six machine learning classifiers, performs SHAP wavelength importance analysis, and produces a landscape-scale probability map of *L. argenteum* occurrence across Table Mountain National Park.

> ⚠️ Generating the probability map tiles for the first time requires downloading and processing all AVIRIS-NG tiles. This can take several hours depending on your machine and internet connection. Completed tiles are cached locally so subsequent runs are fast.

**What it does:**
1. Loads the spectral library CSV and removes bad bands (water absorption windows at 1340–1445 nm and 1790–1965 nm)
2. Vector-normalises each spectrum to remove illumination variation and retain spectral shape
3. Plots spectral profiles for all 26 species (Figure 5 in the paper)
4. Computes the Inter-Species Spectral Angle Matrix (SAM) with permutation p-values
5. Reduces dimensionality using PCA (10 components, retaining >95% of spectral variance)
6. Trains and evaluates six classifiers using 5-fold stratified cross-validation: Logistic Regression, k-NN, SVM (Linear), SVM (RBF), Random Forest, and XGBoost
7. Computes bootstrapped 95% confidence intervals (n=1,000) on F1 and sensitivity
8. Runs SHAP analysis on the XGBoost model to identify which wavelengths drive *L. argenteum* classification
9. Applies the best classifier tile-by-tile across all AVIRIS scenes to produce a probability map
10. Applies a LiDAR canopy height mask (RH98 < 1 m → probability zeroed) to suppress false positives from low shrubland
11. Clips the final map to the Table Mountain National Park boundary

**Output:** Probability map GeoTIFFs, all classification figures, and SHAP importance plots

---

## Key Results

| Metric | Value |
|---|---|
| Spectral library | 26 species · 1,229 spectra |
| LiDAR height accuracy | R² = 0.662 · RMSE = 1.41 m |
| Best classifier | XGBoost |
| *L. argenteum* F1-score | 0.631 (95% CI: 0.519–0.733) |
| *L. argenteum* sensitivity | 0.70 |
| LiDAR mask effect | 29.6% of pixels zeroed |
| Key SHAP wavelengths | 1469 nm · 743–793 nm · 1100–1150 nm |

---

## Citation

If you use this code or pipeline in your work, please cite:

```
Rutlokoane, P., O'Farrell, P., & Blanchard, R. (under review).
Monitoring Leucadendron argenteum populations: exploring opportunities offered by emerging technologies. JGR Biogeosciences.
```

---

## Acknowledgements

Field data collection and analyses were conducted with the support of the University of the Western Cape and the South African Environmental Observation Network (SAEON). Remote sensing data were provided by NASA's BioSCape campaign (2023).

---

## License

This repository is licensed under the **MIT License** — see [LICENSE](LICENSE) for details.

You are free to use, adapt, and build on this code for your own research, provided you include the original attribution.
