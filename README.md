# Monitoring *Leucadendron argenteum* populations: exploring opportunities offered by emerging technologies

This repository contains the analytical workflow for my Honours research project investigating the spectral properties and remote sensing of *Leucadendron argenteum* (Silver Tree), a rare and iconic plant species of South Africa’s Cape Floristic Region. The project integrates hyperspectral imagery and airborne LiDAR data to characterise species-specific reflectance behaviour, canopy structure, and spatial distribution.

All analyses are based on data acquired during the **[BioSCape Campaign (2023)](https://www.bioscape.io/)** and are designed to be reproducible, modular, and reusable for similar species-level remote sensing studies.

---

## Scientific Context and Objectives

*Leucadendron argenteum* presents an unusually strong spectral signal due to its leaf morphology and surface reflectance, making it a compelling candidate for species-level detection using imaging spectroscopy. This project aims to:

- construct a species-specific spectral library,
- quantify structural attributes using LiDAR-derived metrics, and
- integrate hyperspectral and structural information to support detection, mapping, and ecological interpretation.

The repository documents a workflow that can be adapted to other species in the Cape Floristic Region.

---

## Repository Structure and Workflow Order

The repository is organised to reflect the conceptual and analytical progression of the project. **Scripts and notebooks are intended to be read and executed in the following order:**

1. **Spectral Library**  
   Development of a curated spectral library for *L. argenteum*, including preprocessing, quality control, and exploratory spectral analysis.

2. **LiDAR Analysis**  
   Extraction and analysis of canopy height and structural metrics derived from LVIS LiDAR data, supporting structural differentiation and ecological context.

3. **Hyperspectral Analysis**  
   Application of imaging spectroscopy techniques to AVIRIS-NG data, including spectral matching, dimensionality reduction, and spatial mapping.

Each folder contains the relevant notebooks, scripts, and input data references required for that stage of the workflow.

---

## Data Sources

Due to file size and licensing constraints, raw LiDAR and Hyperspectral data are **not** hosted in this repository. Users are expected to download the data independently from the original providers.

Primary datasets used in this project include:

- **AVIRIS-NG BioSCape 2023 Campaign**  
  Airborne Visible/Infrared Imaging Spectrometer – Next Generation  
  https://popo.jpl.nasa.gov/avng/y23_bioscape/

- **BioSCape NetCDF Data Archive** (To be added after updating) 
  https://popo.jpl.nasa.gov/pub/bioscape_netCDF/

- **LVIS L2 Geolocated Surface Elevation and Canopy Height Product (V001)**  
  NASA LVIS Facility, NSIDC DAAC  
  [https://search.earthdata.nasa.gov/](https://search.earthdata.nasa.gov/search?q=LVIS&qt=2023-10-20T00%3A00%3A00.000Z%2C2023-11-15T23%3A59%3A59.999Z&fi=LVIS-Camera!LVIS&fdc=National%2BSnow%2Band%2BIce%2BData%2BCenter%2BDistributed%2BActive%2BArchive%2BCenter%2B%2528NSIDC%2BDAAC%2529&fpj=BioSCape&_ga=2.63606251.53278573.1721067437-1362124365.1714395655&_gl=1*q0ghrk*_ga*MTM2MjEyNDM2NS4xNzE0Mzk1NjU1*_ga_LQ2P0SNJCZ*MTcyMTU5NTQ4Ni4xMjMuMS4xNzIxNTk1NjkxLjAuMC4w)

Users should ensure that directory paths within notebooks are updated to reflect their local data storage structure.

---

## Reproducibility and Outputs

Output files (maps, tables, derived rasters) are intentionally excluded from the repository. The expectation is that users will reproduce outputs locally by running the provided code against the original datasets. This keeps the repository lightweight while also preserving full analytical transparency.

---

## Getting Started

1. Clone the repository:
   ```bash
   git clone https://github.com/Phemelo-R/BioScape-Analysis.git

---

## Citation & Acknowledgements

If you use the data or code, please cite the relevant data providers and acknowledge the AVIRIS-NG Bioscape campaign and NASA JPL. Thank you.

---

*Project by Phemelo Rutlokoane, University of the western Cape, 2025.*

