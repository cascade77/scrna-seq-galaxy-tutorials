# scRNA-seq Galaxy Tutorials

Single-cell RNA-seq pipeline: 10X preprocessing on Galaxy, Scanpy clustering, and AnnData exploration.

## Overview

This repository documents a complete single-cell RNA-seq analysis pipeline, carried out across three tutorials. Starting from raw 10X Chromium FASTQ reads, the pipeline goes through alignment, cell barcode filtering, quality control, dimensionality reduction, clustering, and exploration of the AnnData format.

All Galaxy steps were run on usegalaxy.eu. Scanpy and AnnData notebooks were run on Google Colab.

## Repository Structure
```
.
├── 01_tenx_preprocessing/       # Tutorial 1: STARsolo + DropletUtils on Galaxy
│   ├── outputs/                 # Filtered count matrix (barcodes, features, matrix) + plots
│   └── README.md
├── 02_basic_scrna_scanpy/       # Tutorial 2: Scanpy preprocessing & clustering
│   ├── outputs/                 # QC, PCA, UMAP, clustering plots
│   ├── basic-scrna-tutorial.ipynb
│   └── README.md
├── 03_anndata_tutorial/         # Tutorial 3: AnnData format exploration
│   ├── outputs/
│   ├── getting-started.ipynb
│   └── README.md
└── README.md
```

## Sections

### 1. Pre-processing of 10X Single-Cell RNA Datasets
Raw PBMC FASTQ reads processed through STARsolo alignment and DropletUtils cell filtering on Galaxy.
 [Tutorial](https://training.galaxyproject.org/training-material/topics/single-cell/tutorials/scrna-preprocessing-tenx/tutorial.html)

### 2. Basic scRNA-seq Tutorial (Scanpy)
Filtered count matrix taken through QC, normalization, PCA, UMAP, and Leiden clustering using Scanpy.
[Tutorial](https://github.com/scverse/scanpy-tutorials/blob/main/basic-scrna-tutorial.ipynb)

### 3. AnnData Tutorial
Exploration of the AnnData format covering construction, metadata, layers, and on-disk storage.
[Tutorial](https://anndata.readthedocs.io/en/latest/tutorials/notebooks/getting-started.html)

## Platform

| Step | Platform |
|------|----------|
| Tutorial 1 | usegalaxy.eu |
| Tutorial 2 | Google Colab |
| Tutorial 3 | Google Colab |

## Tools

| Tool | Version | Purpose |
|------|---------|---------|
| RNA STARsolo | 2.7.11a+galaxy0 | Alignment + UMI counting |
| DropletUtils | 1.22.0+galaxy0 | Cell barcode filtering |
| MultiQC | 1.25.1+galaxy3 | QC report aggregation |
| Scanpy | 1.10.x | scRNA-seq analysis |
| AnnData | 0.11.x | Annotated data format |

## Notes
- For detailed information, see the individual readme in directories.
