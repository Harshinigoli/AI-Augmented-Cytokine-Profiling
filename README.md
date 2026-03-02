# AI-Augmented-Cytokine-Profiling
Transformer-integrated cytokine architecture mapping in ILD using single-cell RNA-seq. Combines Scanpy-based immune program scoring with Hugging Face sentence-transformer embeddings to prioritize disease-elevated cytokines along monocyte recruitment and IFN/Th1 axes, enabling interpretable AI-assisted biomarker discovery.
# Transformer-Integrated Cytokine Architecture Mapping (ILD scRNA-seq)

This repository contains an end-to-end, reproducible workflow to (1) map cytokine program architecture in human lung single-cell RNA-seq and (2) add an interpretable **Hugging Face transformer embedding layer** to prioritize disease-elevated cytokines along mechanistic immune axes.

**Dataset:** Human lung scRNA-seq (GEO: **GSE122960**)  
**Cohorts / groups in this notebook:** Control (Healthy) vs ILD subtypes (Recipient): IPF, SSc-ILD, HP, Myositis-ILD, Cryobiopsy  
**Core idea:** Combine quantitative scRNA cytokine module scoring with **transformer-based semantic relevance scoring** to generate a disease-context-aware cytokine shortlist and visually localize those signals on UMAP.

---

## What this project does

### 1) scRNA cytokine architecture (Scanpy)
- Loads `GSE122960_combined_all.h5ad`
- QC filtering + log-normalization
- HVG selection, PCA, neighbors, Leiden clustering, UMAP
- Defines cytokine programs and assigns a **dominant cytokine program per cluster**
  - Monocyte recruitment axis (e.g., CCL2/CCL7/CCR2)
  - IFN/Th1 axis (e.g., CXCL9/10/11, CXCR3)
  - Inflammasome/IL1 axis (IL1B)
  - Neutrophil chemokine axis (CXCL1/2/3/8, CSF3)
  - Fibrosis/TGF axis (TGFB1, CXCL12, CXCL13; plus optional ECM context)

### 2) Transformer-assisted cytokine prioritization (Hugging Face)
- Uses **sentence-transformer embeddings** to convert:
  - a disease context description (ILD/IPF immune narrative)
  - cytokine “functional cards” (short standardized descriptions)
  into vectors
- Computes **cosine similarity** to rank cytokines by semantic alignment to:
  - `MONOCYTE_RECRUITMENT`
  - `IFN_TH1_AXIS`
- Builds an **HF-weighted cytokine activity score** per cell and overlays it on UMAP:
  - `HF_CytokineScore`
  - `HF_MonocyteAxis`
  - `HF_IFNAxis`

This adds a **context-aware and scalable prioritization layer** on top of standard expression-based scoring (i.e., not just “highest fold-change”).

---

## Key outputs

The notebook generates:
- **UMAPs**
  - Clusters (Leiden)
  - Healthy vs Recipient / disease subtype
  - Program score overlays
  - HF-weighted cytokine score overlays
- **Tables (CSV)**
  - Cytokine mean expression and % expressing by cluster (optional)
  - Cytokine program proportions by group (Healthy vs Recipient; and per ILD subtype)
  - Transformer semantic ranking table:
    - gene, MONOCYTE_RECRUITMENT, IFN_TH1_AXIS, combined_score
