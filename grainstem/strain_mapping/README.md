# grainstem · strain_mapping

Intragrain **strain mapping** from 4D-STEM NBD datasets, built on [py4DSTEM](https://github.com/py4dstem/py4DSTEM). Precision is the focus: reciprocal-lattice reflections are localized from the **autocorrelation** of each nanobeam pattern and refined with **subpixel upsampling**, giving robust basis vectors for strain fitting.

## Method — `01_strain_analysis.ipynb`

1. Load the 4D-STEM datacube; bin and crop to a single grain (real-space ROI).
2. Isolate one grain with the same NMF-based segmentation used in `grain_segmentation`.
3. Compute the **autocorrelation** of each nanobeam diffraction pattern to enhance the reciprocal-lattice reflections.
4. Detect Bragg-disk positions by template (cross-correlation) matching with **subpixel refinement** (`upsample_factor`), keeping only reflections within a reference passband built from the grain core.
5. Measure and correct the diffraction origin (scan-drift plane).
6. Fit two reciprocal-lattice basis vectors (g1, g2) across the grain and compute the strain tensor (εxx, εyy, εxy) and lattice rotation (θ) relative to a grain-interior reference lattice; scan positions lacking the reference reflections are excluded.
7. Report the intragrain strain distribution as the standard deviation of the strain-component histograms (~0.5–1%).

## Usage

Set the input path in the configuration cell to your `.raw` / `.h5` dataset and run top to bottom. Adjust, as annotated, the ROI crop, the selected NMF component, and the basis-vector indices (g1, g2).

## Outputs (`results/`)

Strain-component maps (εxx, εyy, εxy, θ), tensile/compressive region overlays, and strain-component histograms.
