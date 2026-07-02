# grainstem · grain_segmentation

NMF-based grain segmentation and grain-by-grain **{111}c planar-defect classification** for 4D-STEM NBD datasets. `01_grain_segmentation` is the main pipeline; `02_defect_classification` is an auxiliary classification / visualization / statistics step applied to the segmentation output.

For each ROI, a near-parallel nanobeam diffraction pattern is recorded at every scan position.

## `01_grain_segmentation.ipynb` (main)

1. Load the 4D-STEM datacube and mask the direct beam.
2. Binarize the diffraction patterns so that only peak positions act as features.
3. Decompose the scan with non-negative matrix factorization (NMF); each component corresponds approximately to one grain orientation.
4. Convert components into individual grains (island decomposition), then merge over-segmented fragments using real-space adjacency together with reciprocal-space peak similarity.
5. Clean small spurious regions and save the finalized grain-label map as a workspace state (`results/<dataset>_state.pkl`).

## `02_defect_classification.ipynb` (auxiliary)

1. Load the Part-1 state.
2. Mark grains classified as defect-bearing. Classification is by manual inspection: enter the indices of the defect-bearing grains in `defect_indices`. The criterion is up to the user; in the accompanying paper a grain was judged defect-bearing when its representative NBD pattern showed a {111}c diffraction peak with a streak along the same direction.
3. Generate virtual bright-field, dark-field, and HAADF images overlaid with the defect mask and grain boundaries.
4. Compute the per-ROI defect-grain **count ratio** and **area ratio**, save them to `results/statistics.json`, and aggregate the ROIs per composition into a Gua20-vs-Gua25 summary (mean ± standard deviation).

## Usage

Set `dataset_name` to the ROI file (e.g. `dataset_name = "Gua25_ROI3"` for `data/Gua25_ROI3.h5`), run `01_` then `02_`.

## Outputs (`results/`)

Grain-label maps, virtual-image overlays, per-grain diffraction-pattern galleries, and per-ROI statistics (`statistics.json`).
