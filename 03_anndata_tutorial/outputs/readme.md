# Tutorial 3: Getting Started with AnnData

## Overview

Exploration of the AnnData format covering construction from scratch, metadata annotation, observation and variable level matrices, layers, conversion to DataFrames, and on-disk storage.

## Topics Covered

- Initializing an AnnData object
- Adding aligned metadata (obs, var)
- Observation and variable level matrices (obsm, varm)
- Unstructured metadata (uns)
- Layers
- Conversion to DataFrames
- Writing and reading .h5ad files
- Partial reading of large data

## Tools

| Tool | Version | Purpose |
|------|---------|---------|
| AnnData | 0.11.x | Annotated data format |
| NumPy | latest | Array operations |
| Pandas | latest | DataFrame operations |
| SciPy | latest | Sparse matrix support |

## Outputs

The output `anndata_tutorial_output.h5ad` file exceeds GitHub's 100 MB file size limit and is therefore not tracked in this repository. The file was successfully generated locally by running the full AnnData getting started notebook on Google Colab.

## Loading the Output

```python
import anndata as ad

adata = ad.read_h5ad('anndata_tutorial_output.h5ad')
print(adata)
```

## Platform

Run on Google Colab.

## Tutorial References

- https://anndata.readthedocs.io/en/latest/tutorials/notebooks/getting-started.html
- https://scverse-tutorials.readthedocs.io/en/latest/notebooks/anndata_getting_started.html
