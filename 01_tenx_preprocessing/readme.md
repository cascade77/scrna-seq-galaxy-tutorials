# Tutorial 1: Pre-processing of 10X Single-Cell RNA Datasets

## Overview

Raw FASTQ reads from a PBMC 1k v3 10X Chromium dataset processed on Galaxy through spliced alignment, UMI counting, and cell barcode filtering. The goal is to go from raw sequencing reads to a high-quality filtered count matrix suitable for downstream single-cell analysis.

The dataset consists of reads from two sequencing lanes of the same library. Each lane contributes a pair of FASTQ files: R1 contains the 28 bp cell barcode and UMI sequence characteristic of 10X Chromium v3 chemistry, and R2 contains the cDNA sequence. Alignment is performed against the hg19 reference genome using the Homo_sapiens.GRCh37.75 GTF annotation.


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
Raw FASTQs (2 lanes, R1 + R2)
|
RNA STARsolo — spliced alignment to hg19, UMI counting per gene per cell
using the 10X v3 barcode whitelist; outputs raw MTX + barcode/gene lists
|
MultiQC — aggregates STARsolo log to confirm mapping quality
|
DropletUtils DefaultDrops — filters barcodes above the knee of the
barcode rank plot; expected cells: 3000
|
DropletUtils Rank Barcodes — generates knee plot to visualize the
inflection and knee points of the barcode rank curve
|
DropletUtils EmptyDrops — statistical test distinguishing real cells
from empty droplets; lower bound 200, FDR threshold 0.01
|
Filtered MTX (barcodes.tsv, features.tsv, matrix.mtx)
```
## Tools

| Tool | Version | Purpose |
|------|---------|---------|
| RNA STARsolo | 2.7.11a+galaxy0 | Spliced alignment + UMI counting |
| MultiQC | 1.25.1+galaxy3 | Mapping QC report |
| DropletUtils | 1.22.0+galaxy0 | Cell filtering |

## Key Parameters

| Parameter | Value |
|-----------|-------|
| Chemistry | 10X Chromium v3 |
| Reference genome | hg19 (GRCh37) |
| Gene annotation | Homo_sapiens.GRCh37.75.gtf |
| UMI deduplication | CellRanger2-4 algorithm |
| UMI filtering | Remove UMIs with N and homopolymers |
| Barcode matching | Multiple matches, 1MM_multi (CellRanger 2) |
| Strandedness | Read strand same as original RNA molecule |
| DefaultDrops expected cells | 3000 |
| EmptyDrops lower bound | 200 |
| EmptyDrops FDR threshold | 0.01 |

## Results

MultiQC confirmed ~87.5% uniquely mapped reads, which is expected and acceptable for 10X datasets. STARsolo detected 5200 total cell barcodes of which 272 passed the quality threshold. EmptyDrops statistical filtering further refined this to a set of high-confidence cells. The final filtered count matrix has 272 cells.

## Outputs

| File | Description |
|------|-------------|
| outputs/barcodes.tsv | Filtered cell barcodes |
| outputs/features.tsv | Gene list (Ensembl ID + gene symbol) |
| outputs/matrix.mtx | Filtered count matrix in Matrix Market format |
| outputs/knee_plot.png | Barcode rank plot with knee and inflection points |
| outputs/emptydrops_plot.png | EmptyDrops cell detection plot |

## Plots

<table>
  <tr>
    <td align="center"><img src="outputs/knee_plot.png"/><br/>Barcode Rank Plot: the knee and inflection points mark the boundary between real cells and empty droplets</td>
    <td align="center"><img src="outputs/emptydrops_plot.png"/><br/>EmptyDrops Plot: red dots indicate barcodes called as real cells by the statistical test</td>
  </tr>
</table>

## Platform

Run on usegalaxy.eu.

## Tutorial Reference

https://training.galaxyproject.org/training-material/topics/single-cell/tutorials/scrna-preprocessing-tenx/tutorial.html

## Citations

Dobin et al. (2013) STAR: ultrafast universal RNA-seq aligner. *Bioinformatics*. https://doi.org/10.1093/bioinformatics/bts635

Lun et al. (2019) EmptyDrops: distinguishing cells from empty droplets in droplet-based single-cell RNA sequencing data. *Genome Biology*. https://doi.org/10.1186/s13059-019-1662-y
