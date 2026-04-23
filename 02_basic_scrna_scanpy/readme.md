# Tutorial 2: Basic scRNA-seq Analysis with Scanpy

## Overview

Bone marrow mononuclear cells from two healthy donors (s1d1 and s1d3) from the OpenProblems NeurIPS 2021 benchmarking dataset, processed through a complete Scanpy preprocessing and clustering pipeline. The data was measured using the 10X Multiome Gene Expression kit. After concatenation, the dataset contains 17,125 cells across 36,601 genes.

The pipeline covers QC metric computation, doublet detection with Scrublet, cell and gene filtering, count depth normalization, log transformation, highly variable gene selection, PCA, nearest neighbor graph construction, UMAP embedding, Leiden clustering at multiple resolutions, and cell type annotation using both curated marker genes and data-driven differentially expressed genes.

## Input Data

| Sample | Cells | Description |
|--------|-------|-------------|
| s1d1 | 8,785 | Bone marrow MNCs, donor 1 day 1 |
| s1d3 | 8,340 | Bone marrow MNCs, donor 1 day 3 |

Source: doi:10.6084/m9.figshare.22716739.v1

Data was loaded using `sc.read_10x_h5` and concatenated with `ad.concat`.

## Pipeline

```
Two 10X h5 files (s1d1, s1d3)
|
Load + concatenate — sc.read_10x_h5, ad.concat with sample label
|
QC metrics — sc.pp.calculate_qc_metrics with mt, ribo, hb gene flags
flagging MT- (mitochondrial), RPS/RPL (ribosomal), HB (hemoglobin) genes
|
Doublet detection — sc.pp.scrublet per batch; adds doublet_score
and predicted_doublet to .obs
|
Cell filtering — sc.pp.filter_cells(min_genes=100)
Gene filtering — sc.pp.filter_genes(min_cells=3)
|
Normalization — sc.pp.normalize_total (median count depth)
Log transform — sc.pp.log1p
|
Highly variable genes — sc.pp.highly_variable_genes
n_top_genes=2000, batch_key="sample"
|
PCA — sc.tl.pca (50 components)
|
Nearest neighbors — sc.pp.neighbors
|
UMAP — sc.tl.umap
|
Leiden clustering — sc.tl.leiden at resolutions 0.02, 0.50, 2.00
using igraph flavor with n_iterations=2
|
Marker gene annotation — sc.pl.dotplot with curated marker gene dict
|
Rank genes — sc.tl.rank_genes_groups (Wilcoxon test per cluster)
Visualize — sc.pl.rank_genes_groups_dotplot, sc.pl.umap per top gene
```
## Tools

| Tool | Version | Purpose |
|------|---------|---------|
| Scanpy | 1.12.1 | Full scRNA-seq analysis |
| AnnData | 0.12.10 | Data structure |
| leidenalg | 0.11.0 | Leiden graph clustering |
| igraph | 1.0.0 | Graph backend |
| scrublet | latest | Doublet detection |


## Key Parameters

| Parameter | Value |
|-----------|-------|
| Min genes per cell | 100 |
| Min cells per gene | 3 |
| Normalization target | Median total counts |
| Highly variable genes | 2000 (batch-aware) |
| PCA components | 50 |
| Leiden resolutions | 0.02, 0.50, 2.00 |
| DE test | Wilcoxon rank-sum |

## Marker Genes Used for Annotation

| Cell Type | Marker Genes |
|-----------|-------------|
| CD14+ Mono | FCN1, CD14 |
| CD16+ Mono | TCF7L2, FCGR3A, LYN |
| cDC2 | CST3, COTL1, LYZ, CLEC10A, FCER1A |
| Erythroblast | MKI67, HBA1, HBB |
| NK | GNLY, NKG7, CD247, FCER1G, TYROBP |
| Naive CD20+ B | MS4A1, IL4R, IGHD, FCRL1, IGHM |
| Plasma cells | MZB1, HSP90B1, FNDC3B, IGKC, JCHAIN |
| CD4+ T | CD4, IL7R, TRBC2 |
| CD8+ T | CD8A, CD8B, GZMK, GZMA, CCL5 |
| T naive | LEF1, CCR7, TCF7 |
| pDC | GZMB, IL3RA, COBLL1, TCF4 |


## Outputs

| File | Description |
|------|-------------|
| outputs/pbmc_tutorial_output.h5ad | Final AnnData object (not tracked, 454 MB, exceeds GitHub limit) |
| outputs/qc_violin.jpeg | Violin plots of n_genes_by_counts, total_counts, pct_counts_mt |
| outputs/qc_scatter.jpeg | Scatter: total_counts vs n_genes_by_counts colored by pct_counts_mt |
| outputs/highly_variable_genes.jpeg | HVG selection: normalized vs raw dispersion vs mean expression |
| outputs/pca_variance_ratio.jpeg | Variance explained per PC (elbow plot, log scale) |
| outputs/pca_plot.jpeg | PCA colored by sample and pct_counts_mt across PC1-4 |
| outputs/umap_sample.jpeg | UMAP colored by sample (s1d1 vs s1d3) |
| outputs/umap_leiden.jpeg | UMAP colored by Leiden clusters (default resolution) |
| outputs/umap_doublets.jpeg | UMAP showing predicted doublets and doublet scores |
| outputs/umap_qc_metrics.jpeg | UMAP colored by log1p_total_counts, pct_counts_mt, log1p_n_genes |
| outputs/umap_leiden_resolutions.jpeg | UMAP at resolutions 0.02, 0.50, and 2.00 |
| outputs/dotplot_known_markers.jpeg | Dotplot of curated marker genes across coarse clusters |
| outputs/dotplot_ranked_genes.jpeg | Dotplot of top 5 DE genes per cluster from Wilcoxon test |
| outputs/umap_marker_genes.jpeg | UMAP of top DE genes for cluster 7 (NK cells: NKG7, GNLY, KLRD1, PRF1, CST7) |

## Results

### QC Metrics

<table>
  <tr>
    <td align="center"><img src="outputs/qc_violin.jpeg"/><br/>Violin plots of n_genes_by_counts, total_counts, and pct_counts_mt across all cells before filtering</td>
    <td align="center"><img src="outputs/qc_scatter.jpeg"/><br/>Scatter plot of total counts vs genes per cell colored by mitochondrial percentage</td>
  </tr>
</table>

### Highly Variable Genes and PCA

<table>
  <tr>
    <td align="center"><img src="outputs/highly_variable_genes.jpeg"/><br/>Highly variable gene selection showing normalized dispersion vs mean expression. Black dots are the selected top 2000 HVGs</td>
    <td align="center"><img src="outputs/pca_variance_ratio.jpeg"/><br/>Variance ratio per PC on log scale. The elbow around PC 10-15 guides the choice of n_pcs for neighbor graph construction</td>
  </tr>
</table>

<table>
  <tr>
    <td align="center"><img src="outputs/pca_plot.jpeg"/><br/>PCA colored by sample (top) and pct_counts_mt (bottom) across PC1-2 and PC3-4. No strong batch effect is visible between s1d1 and s1d3</td>
  </tr>
</table>

### UMAP

<table>
  <tr>
    <td align="center"><img src="outputs/umap_sample.jpeg"/><br/>UMAP colored by sample. The two donors mix well across most clusters, indicating minimal batch effect</td>
    <td align="center"><img src="outputs/umap_leiden.jpeg"/><br/>UMAP colored by Leiden clusters at default resolution showing 26 communities</td>
  </tr>
  <tr>
    <td align="center"><img src="outputs/umap_doublets.jpeg"/><br/>UMAP showing predicted doublets (orange) and doublet score. Doublets are sparse and distributed, not forming distinct clusters</td>
    <td align="center"><img src="outputs/umap_qc_metrics.jpeg"/><br/>UMAP colored by QC metrics: total counts, mitochondrial percentage, and genes per cell</td>
  </tr>
</table>

<table>
  <tr>
    <td align="center"><img src="outputs/umap_leiden_resolutions.jpeg"/><br/>Leiden clustering at resolutions 0.02 (5 coarse groups), 0.50 (17 clusters), and 2.00 (35 fine clusters). Resolution 0.50 was used for annotation</td>
  </tr>
</table>

### Marker Genes and Cell Type Annotation

<table>
  <tr>
    <td align="center"><img src="outputs/dotplot_known_markers.jpeg"/><br/>Dotplot of curated marker genes across coarse Leiden clusters (res 0.02). Dot size shows fraction of cells expressing the gene; color shows mean expression</td>
    <td align="center"><img src="outputs/dotplot_ranked_genes.jpeg"/><br/>Dotplot of top 5 Wilcoxon DE genes per cluster at resolution 0.50</td>
  </tr>
</table>

<table>
  <tr>
    <td align="center"><img src="outputs/umap_marker_genes.jpeg"/><br/>UMAP of top DE genes for cluster 7: NKG7, GNLY, KLRD1, PRF1, CST7, confirming cluster 7 as NK cells</td>
  </tr>
</table>

## Note on Output File

The final `pbmc_tutorial_output.h5ad` file (454 MB) exceeds GitHub's 100 MB file size limit and is therefore not tracked in this repository. The file was successfully generated locally by running the full Scanpy tutorial notebook on Google Colab.

## Platform

Run on Google Colab.

## Tutorial Reference

https://github.com/scverse/scanpy-tutorials/blob/main/basic-scrna-tutorial.ipynb

## Citations

Luecken et al. (2021) Benchmarking atlas-level data integration in single-cell genomics. *Nature Methods*. https://doi.org/10.1038/s41592-021-01336-8

McCarthy et al. (2017) Scater: pre-processing, quality control, normalization and visualization of single-cell RNA-seq data in R. *Bioinformatics*. https://doi.org/10.1093/bioinformatics/btw777

Satija et al. (2015) Spatial reconstruction of single-cell gene expression data. *Nature Biotechnology*. https://doi.org/10.1038/nbt.3192

Stuart et al. (2019) Comprehensive Integration of Single-Cell Data. *Cell*. https://doi.org/10.1016/j.cell.2019.05.031

Traag et al. (2019) From Louvain to Leiden: guaranteeing well-connected communities. *Scientific Reports*. https://doi.org/10.1038/s41598-019-41695-z

Wolock et al. (2019) Scrublet: Computational Identification of Cell Doublets in Single-Cell Transcriptomic Data. *Cell Systems*. https://doi.org/10.1016/j.cels.2018.11.005

Zheng et al. (2017) Massively parallel digital transcriptional profiling of single cells. *Nature Communications*. https://doi.org/10.1038/ncomms14049
