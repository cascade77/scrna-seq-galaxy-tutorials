# Tutorial 3: Getting Started with AnnData

## Overview

Hands-on introduction to AnnData ("Annotated Data"), the standard data structure for single-cell analysis in Python. AnnData is designed for matrix-like data where both rows (observations) and columns (variables) are indexed and can carry associated metadata. In scRNA-seq, rows correspond to cells with barcodes and columns correspond to genes with IDs.

The tutorial works through construction of an AnnData object from scratch, all major annotation slots, on-disk storage, and the distinction between views and copies. AnnData version 0.12.10 was used.

## What AnnData Handles

AnnData was designed to be the single container that handles all of the following, which no standard Python data structure covers cleanly on its own:

- Sparse count matrices efficiently (CSR/CSC format via SciPy)
- Unstructured metadata of any type
- Observation-level and variable-level metadata as Pandas DataFrames
- Multi-dimensional embeddings like UMAP and PCA coordinates
- Multiple representations of the same data (raw counts, normalized, log-transformed) via layers

## Tutorial Walkthrough

### Initializing AnnData

A sparse count matrix of shape 100 cells × 2000 genes is created using a Poisson distribution and wrapped in an AnnData object:

```python
import numpy as np
import pandas as pd
import anndata as ad
from scipy.sparse import csr_matrix

counts = csr_matrix(np.random.poisson(1, size=(100, 2000)), dtype=np.float32)
adata = ad.AnnData(counts)
```

The obs and var axes are then indexed with string names:

```python
adata.obs_names = [f"Cell_{i:d}" for i in range(adata.n_obs)]
adata.var_names = [f"Gene_{i:d}" for i in range(adata.n_vars)]
```

AnnData supports subsetting just like a Pandas DataFrame, returning views rather than copies:

```python
adata[["Cell_1", "Cell_10"], ["Gene_5", "Gene_1900"]]
```

### Adding Observation and Variable Metadata

`adata.obs` and `adata.var` are Pandas DataFrames. Adding a column is straightforward:

```python
ct = np.random.choice(["B", "T", "Monocyte"], size=(adata.n_obs,))
adata.obs["cell_type"] = pd.Categorical(ct)
```

Categoricals are preferred over plain strings for memory efficiency. Cells can then be subset by metadata:

```python
bdata = adata[adata.obs.cell_type == "B"]
```

### Observation and Variable Level Matrices (obsm, varm)

Multi-dimensional metadata such as UMAP coordinates or gene loadings are stored in `.obsm` and `.varm`. The only constraint is that `.obsm` arrays must have length equal to `n_obs` and `.varm` arrays must have length equal to `n_vars`:

```python
adata.obsm["X_umap"] = np.random.normal(0, 1, size=(adata.n_obs, 2))
adata.varm["gene_stuff"] = np.random.normal(0, 1, size=(adata.n_vars, 5))
```

### Unstructured Metadata (uns)

Any Python object (list, dict, array) can be stored in `.uns`:

```python
adata.uns["random"] = [1, 2, 3]
```

### Layers

Multiple representations of the core data matrix are stored as layers. For example, storing log-transformed counts alongside raw counts:

```python
adata.layers["log_transformed"] = np.log1p(adata.X)
```

The full AnnData object at this point:

```
AnnData object with n_obs × n_vars = 100 × 2000
obs: 'cell_type'
uns: 'random'
obsm: 'X_umap'
varm: 'gene_stuff'
layers: 'log_transformed
```
### Conversion to DataFrames

Any layer can be exported as a Pandas DataFrame with obs_names as the row index and var_names as the column index:

```python
adata.to_df(layer="log_transformed")
```

### Writing to Disk

AnnData uses the h5ad format, an HDF5-based binary format. Writing with gzip compression:

```python
adata.write('my_results.h5ad', compression="gzip")
```

### Views and Copies

Subsetting AnnData always returns a view, not a copy. A view holds no new data in memory and references the original object. Calling `.copy()` on a view produces an independent AnnData object:

```python
adata_subset = adata[:5, ['Gene_1', 'Gene_3']].copy()
```

Modifying any attribute of a view (such as adding a column to `.obs`) automatically triggers an implicit copy:

```python
adata_subset = adata[:3, ['Gene_1', 'Gene_2']]
adata_subset.obs['foo'] = range(3)
# adata_subset is now an actual AnnData, no longer a view
```

Values inside a view can also be set directly, which modifies the underlying data:

```python
adata[:3, 'Gene_1'].X = [0, 0, 0]
```

### Rebuilding with Full Metadata

The tutorial also shows how to replace `.obs` entirely with a richer DataFrame built from external metadata while keeping the same count matrix:

```python
obs_meta = pd.DataFrame({
    'time_yr': np.random.choice([0, 2, 4, 8], adata.n_obs),
    'subject_id': np.random.choice(['subject 1', 'subject 2', 'subject 4', 'subject 8'], adata.n_obs),
    'instrument_type': np.random.choice(['type a', 'type b'], adata.n_obs),
    'site': np.random.choice(['site x', 'site y'], adata.n_obs),
}, index=adata.obs.index)

adata = ad.AnnData(adata.X, obs=obs_meta, var=adata.var)
```

## Topics Covered

| Topic | AnnData Slot |
|-------|-------------|
| Count matrix | adata.X |
| Cell metadata | adata.obs |
| Gene metadata | adata.var |
| UMAP / PCA embeddings | adata.obsm |
| Gene loadings | adata.varm |
| Unstructured data | adata.uns |
| Multiple data layers | adata.layers |
| On-disk storage | adata.write / ad.read_h5ad |
| Efficient subsetting | Views vs copies |

## Tools

| Tool | Version | Purpose |
|------|---------|---------|
| AnnData | 0.12.10 | Annotated data format |
| NumPy | 2.0.2 | Array operations |
| Pandas | 2.2.2 | DataFrame metadata |
| SciPy | 1.16.3 | Sparse matrix support |

## Loading the Output

```python
import anndata as ad

adata = ad.read_h5ad('anndata_tutorial_output.h5ad')
print(adata)
```

## Note on Output File

The output `anndata_tutorial_output.h5ad` exceeds GitHub's 100 MB file size limit and is not tracked in this repository. It was successfully generated by running the full notebook on Google Colab.

## Platform

Run on Google Colab.

## Tutorial References

https://anndata.readthedocs.io/en/latest/tutorials/notebooks/getting-started.html

https://scverse-tutorials.readthedocs.io/en/latest/notebooks/anndata_getting_started.html

## Citations

Virshup et al. (2021) anndata: Annotated data. *bioRxiv*. https://doi.org/10.1101/2021.12.16.473007
