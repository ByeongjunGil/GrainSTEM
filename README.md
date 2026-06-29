# 4D-STEM NBD Quantification of {111}c Planar Defects in MA₁₋ₓGuaₓPbI₃

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
2. Mark grains classified as defect-bearing. A grain is counted as defect-bearing only if its representative NBD pattern shows a {111}c diffraction peak together with a streak along the same direction; diffraction patterns unambiguously assignable to δ-phase polytypes (long-range ordering of face-sharing octahedra) are excluded.
3. Generate virtual bright-field, dark-field, and HAADF images overlaid with the defect mask and grain boundaries.
4. Compute the per-ROI defect-grain **count ratio** and **area ratio**, save them to `results/statistics.json`, and aggregate the four ROIs per composition into a Gua20-vs-Gua25 summary (mean ± standard deviation).

## Repository layout

```
.
├── notebooks/
│   ├── Part1_grain_segmentation.ipynb
│   └── Part2_defect_classification_and_stats.ipynb
├── results/        # per-ROI grain maps, overlays, and statistics.json (generated)
├── figures/        # full per-grain diffraction-pattern galleries for ROIs not in the SI
├── data/           # place raw .h5 4D-STEM datasets here (not tracked; see below)
├── requirements.txt
├── LICENSE
└── README.md
```

## Data availability

The raw 4D-STEM NBD datasets (`.h5`) are too large for version control and are **not** included here. They are archived separately (see the Data Availability Statement of the paper); download them and place each file under `data/` as `data/<dataset_name>.h5` (e.g. `data/Gua20_Acq1.h5`).

The `figures/` folder contains the complete per-grain diffraction-pattern galleries for the ROIs that are not shown in the Supporting Information of the paper (i.e. ROI 2–4 of each composition), which were used to classify each grain as defect-bearing or defect-free.

## Usage

1. Install dependencies (a CUDA-capable GPU is recommended for the NMF step; the code falls back to CPU automatically):

   ```bash
   pip install -r requirements.txt
   ```

2. Place the raw dataset for the ROI you want to analyze in `data/`.

3. In `Part1_grain_segmentation.ipynb`, set `dataset_name` (and the matching `data/<name>.h5` path) in the configuration cell, then run all cells. Adjust `n_components` to roughly the expected number of grains in the ROI if needed.

4. In `Part2_defect_classification_and_stats.ipynb`, set the same `dataset_name`, list the defect-bearing grain indices in `defect_indices`, and run all cells. Repeat for each ROI; the final cell aggregates whatever ROIs are present in `results/statistics.json`.

## Notes on reproducibility

- NMF uses random initialization; component indices and the exact over-segmentation can vary slightly between runs, but the merged grains and the resulting defect-grain ratios are stable.
- The mask center/radius, binarization threshold, and merge thresholds in Part 1 are exposed as variables at the top of each cell and may need light per-dataset tuning.

## Citation

If you use this code, please cite the accompanying paper. Citation details will be added upon publication.

## License

Released under the MIT License (see `LICENSE`).
