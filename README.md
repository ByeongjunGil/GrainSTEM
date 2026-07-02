# GrainSTEM

**High-precision 4D-STEM toolkit — NMF grain segmentation, {111}c planar-defect classification, and autocorrelation-based strain mapping with subpixel upsampling.**

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21025176.svg)](https://doi.org/10.5281/zenodo.21025176)

Code accompanying the manuscript *"Design Rules for Quantitative Control of Intragrain Planar Defects in Halide Perovskites"* (B. Gil, S. J. Park, J. Y. Kim, M. Kim).

GrainSTEM provides grain-resolved analysis of low-dose four-dimensional scanning transmission electron microscopy nanobeam-diffraction (4D-STEM NBD) datasets. It bundles two **independent** analyses that share a common NBD preprocessing / NMF-segmentation core:

| Module | Purpose | Docs |
|--------|---------|------|
| **grain_segmentation** | NMF-based grain segmentation and grain-by-grain {111}c planar-defect classification / statistics | [→ README](grainstem/grain_segmentation/README.md) |
| **strain_mapping** | Intragrain strain mapping via autocorrelation + subpixel-upsampling Bragg-disk detection and reference-lattice strain | [→ README](grainstem/strain_mapping/README.md) |

## Installation

A CUDA-capable GPU is recommended for the NMF step (the code falls back to CPU automatically).

```bash
pip install -r requirements.txt
```

## Repository layout

```
.
├── grainstem/
│   ├── grain_segmentation/     # NMF segmentation (main) + defect classification (auxiliary)
│   │   ├── 01_grain_segmentation.ipynb
│   │   ├── 02_defect_classification.ipynb
│   │   └── README.md
│   └── strain_mapping/         # autocorrelation-based intragrain strain mapping
│       ├── 01_strain_analysis.ipynb
│       └── README.md
├── data/        # raw .h5 / .raw 4D-STEM datasets (not tracked; see Data availability)
├── results/     # grain maps, overlays, diffraction-pattern galleries, strain maps, statistics
├── requirements.txt
├── pyproject.toml
├── LICENSE
└── README.md
```

## Data availability

The raw 4D-STEM NBD datasets are too large for version control and are archived on Zenodo:

> **Zenodo:** https://doi.org/10.5281/zenodo.21025176

Download the datasets and place them in `data/`. Set `dataset_name` (segmentation) or the input path (strain) to the corresponding file.

## Citing

If you use GrainSTEM, please cite the manuscript above and the archived software release (Zenodo DOI, pinned to the version you used).

## License

MIT — see [LICENSE](LICENSE).
