# Tutorial 1: Pre-processing of 10X Single-Cell RNA Datasets

## Overview

Raw FASTQ reads from a PBMC 1k v3 10X Chromium dataset processed on Galaxy through alignment, UMI counting, and cell barcode filtering.

## Input Data

| File | Description |
|------|-------------|
| subset_pbmc_1k_v3_S1_L001_R1/R2 | Lane 1 barcode + cDNA reads |
| subset_pbmc_1k_v3_S1_L002_R1/R2 | Lane 2 barcode + cDNA reads |
| Homo_sapiens.GRCh37.75.gtf | Human genome annotation |
| 3M-february-2018.txt.gz | 10X v3 cell barcode whitelist |

Source: https://zenodo.org/record/3457880

## Pipeline

```
Raw FASTQs → RNA STARsolo → MultiQC → DropletUtils (DefaultDrops) → DropletUtils (EmptyDrops) → Filtered MTX
```
## Tools

| Tool | Version | Purpose |
|------|---------|---------|
| RNA STARsolo | 2.7.11a+galaxy0 | Spliced alignment + UMI counting |
| MultiQC | 1.25.1+galaxy3 | Mapping QC report |
| DropletUtils | 1.22.0+galaxy0 | Cell filtering |

## Key Parameters

- Chemistry: 10X Chromium v3
- Reference genome: hg19 (GRCh37)
- UMI deduplication: CellRanger2-4 algorithm
- Cell filtering: EmptyDrops (lower bound 200, FDR 0.01)

## Results

MultiQC confirmed ~87.5% uniquely mapped reads. EmptyDrops filtering retained high-quality cells from the initial droplet pool.

## Outputs

| File | Description |
|------|-------------|
| outputs/barcodes.tsv | Filtered cell barcodes |
| outputs/features.tsv | Gene list |
| outputs/matrix.mtx | Filtered count matrix |
| outputs/knee_plot.png | Barcode rank plot |
| outputs/emptydrops_plot.png | EmptyDrops cell detection plot |

## Plots

| Knee Plot | EmptyDrops Plot |
|-----------|----------------|
| ![Knee Plot](outputs/knee_plot.png) | ![EmptyDrops Plot](outputs/emptydrops_plot.png) |

## Platform

Run on usegalaxy.eu.

## Tutorial Reference

https://training.galaxyproject.org/training-material/topics/single-cell/tutorials/scrna-preprocessing-tenx/tutorial.html
