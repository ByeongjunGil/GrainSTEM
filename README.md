# 4D-STEM NBD Quantification of {111}c Planar Defects in MA₁₋ₓGuaₓPbI₃

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21025176.svg)](https://doi.org/10.5281/zenodo.21025176)

Code accompanying the manuscript *"Design Rules for Quantitative Control of Intragrain Planar Defects in Halide Perovskites"* (B. Gil, S. J. Park, J. Y. Kim, M. Kim).

This repository contains the grain-segmentation and {111}c planar-defect classification pipeline applied to low-dose four-dimensional scanning transmission electron microscopy nanobeam-diffraction (4D-STEM NBD) datasets. It quantifies, on a grain-by-grain basis, the fraction of grains bearing {111}c planar defects in Gua20 (MA₀.₈Gua₀.₂PbI₃) and Gua25 (MA₀.₇₅Gua₀.₂₅PbI₃) films across four regions of interest (ROIs) each.

## Method overview

For each ROI, a near-parallel nanobeam diffraction pattern is recorded at every scan position. The analysis proceeds in two notebooks:

**`notebooks/Part1_grain_segmentation.ipynb`**
1. Load the 4D-STEM datacube and mask the direct beam.
2. Binarize the diffraction patterns so that only peak positions act as features.
3. Decompose the scan with non-negative matrix factorization (NMF); each component corresponds approximately to one grain orientation.
4. Convert components into individual grains (island decomposition), then merge over-segmented fragments using real-space adjacency together with reciprocal-space peak similarity.
5. Clean small spurious regions and save a finalized grain-label map as a workspace state (`results/<dataset>_state.pkl`).

**`notebooks/Part2_defect_classification_and_stats.ipynb`**
1. Load the Part 1 state.
2. Mark grains classified as defect-bearing. Grains are classified as defect-bearing by manual inspection: the user enters the indices of the grains judged to contain planar defects in defect_indices in the Part 2 notebook. The classification criterion is up to the user; in the accompanying paper, a grain was judged defect-bearing when its representative NBD pattern showed a {111}c diffraction peak with a streak along the same direction.
3. Generate virtual bright-field, dark-field, and HAADF images overlaid with the defect mask and grain boundaries.
4. Compute the per-ROI defect-grain **count ratio** and **area ratio**, save them to `results/statistics.json`, and aggregate the four ROIs per composition into a Gua20-vs-Gua25 summary (mean ± standard deviation).

## Repository layout

```
.
├── notebooks/
│   ├── Part1_grain_segmentation.ipynb
│   └── Part2_defect_classification_and_stats.ipynb
├── results/        # per-ROI grain maps, overlays, diffraction-pattern galleries, and statistics
├── data/           # place raw .h5 4D-STEM datasets here (not tracked; see Data availability)
├── requirements.txt
├── LICENSE
└── README.md
```

## Data availability

The raw 4D-STEM NBD datasets (`.h5`, ~6 GB total) are too large for version control and are archived on Zenodo:

> **Zenodo:** https://doi.org/10.5281/zenodo.21025176

Download the eight `.h5` files (`Gua20_ROI1`–`ROI4`, `Gua25_ROI1`–`ROI4`) and place
them in `data/`. To analyze a given ROI, set `dataset_name` in the notebooks to the
corresponding file name (e.g. `dataset_name = "Gua25_ROI3"` for `data/Gua25_ROI3.h5`).

The `results/` folder includes the complete per-grain diffraction-pattern galleries
(`*_all_grains_dps_page_*.png`) for the ROIs that are not reproduced in the Supporting
Information of the paper, together with the grain maps, virtual-image overlays, and
per-ROI statistics used to classify each grain as defect-bearing or defect-free.

## Usage

1. Install dependencies (a CUDA-capable GPU is recommended for the NMF step; the code falls back to CPU automatically):

   ```bash
   pip install -r requirements.txt
   ```

2. Download the raw dataset for the ROI you want to analyze from Zenodo and place it in `data/`.

3. In `Part1_grain_segmentation.ipynb`, set `dataset_name` (and the matching `data/<name>.h5` path) in the configuration cell, then run all cells. Adjust `n_components` to roughly the expected number of grains in the ROI if needed.

4. In `Part2_defect_classification_and_stats.ipynb`, set the same `dataset_name`, list the defect-bearing grain indices in `defect_indices`, and run all cells. Repeat for each ROI; the final cell aggregates whatever ROIs are present in `results/statistics.json`.

## Notes on reproducibility

- NMF uses random initialization; component indices and the exact over-segmentation can vary slightly between runs, but the merged grains and the resulting defect-grain ratios are stable.
- The mask center/radius, binarization threshold, and merge thresholds in Part 1 are exposed as variables at the top of each cell and may need light per-dataset tuning.

## Citation

If you use this code or data, please cite both the accompanying paper (details to be added upon publication) and the Zenodo dataset:

> B. Gil, S. J. Park, J. Y. Kim, M. Kim. *Raw 4D-STEM nanobeam diffraction datasets for {111}c planar-defect quantification in MA₁₋ₓGuaₓPbI₃ perovskites.* Zenodo (2026). https://doi.org/10.5281/zenodo.21025176

## License

Released under the MIT License (see `LICENSE`).
