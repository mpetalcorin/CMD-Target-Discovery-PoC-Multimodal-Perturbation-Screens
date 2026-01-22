# CMD-Target-Discovery-PoC-Multimodal-Perturbation-Screens
This proof-of-concept provides a practical solution, a complete computational workflow that converts multimodal perturbation screening outputs into a ranked list of high-confidence drug target candidates for cardiometabolic disease, with clear supporting evidence and uncertainty estimates.
# CMD Target Discovery PoC
## Multimodal Perturbation Screens → High-Confidence Drug Target Candidates (BioAI)

This repository contains a fully reproducible proof-of-concept (PoC) pipeline that translates **ultra-high throughput perturbation screening outputs** into a ranked shortlist of **high-confidence drug target candidates for cardiometabolic disease (CMD)**.

The pipeline is designed to reflect how modern BioAI target discovery programs operate at scale, integrating:
- **Genome-scale genetic perturbations** (CRISPR KO / CRISPRi-like)
- **Compound perturbation profiling** (small molecules)
- **Cell Painting-like morphology embeddings** (high-content imaging)
- **Perturbational transcriptomics** (DRUG-seq / Perturb-seq-like signatures)
- **Joint multimodal embedding + perturbation similarity graph**
- **Compound→gene phenotype linking (MoA-style reinforcement)**
- **Bayesian evidence integration** to produce **confidence + uncertainty** for each nominated target

> This PoC uses simulated data to demonstrate the full pipeline end-to-end. The same code paths are directly adaptable to real screening outputs.

Cardiometabolic disease is complex, multiple pathways interact, compensate, and shift with context. Ultra-high throughput perturbation screens can measure thousands of gene and compound perturbations, but the true challenge is:

**Turning massive multimodal phenotype data into an actionable ranked list of drug targets, with interpretable supporting evidence.**

This PoC provides an end-to-end, decision-ready blueprint.

## What this repository delivers

### Decision-ready outputs
- **Ranked gene targets** with integrated evidence + Bayesian posterior confidence  
- **Compound→gene links** showing which compounds phenocopy which gene perturbations  
- **Quality control tables** for replicate consistency  
- **Publication-quality figures** summarizing key pipeline stages  
- **Reproducible pipeline code + notebook**

## Repository structure

```text
.
├── cmd_target_discovery_poc.py
├── Novo_BioAI_CMD_Target_Discovery_PoC_Upgraded.ipynb
├── cmd_target_discovery_poc_outputs/
│   ├── meta_raw.csv
│   ├── qc_replicate_consistency.csv
│   ├── compound_gene_links.csv
│   ├── ranked_targets.csv
│   ├── ranked_targets_bayesian.csv
│   ├── model_performance.json
│   └── figures/
│       ├── fig1_qc_replicate_consistency.png
│       ├── fig2_joint_embedding_pca.png
│       ├── fig3_calibration_curve.png
│       ├── fig4_integrated_score_separation.png
│       └── fig5_top_20_targets.png
├── README.md
├── MODEL_CARD.md
└── DATASET_CARD.md
```

# Model Card
## CMD Target Discovery PoC, Multimodal Phenotype AI + Bayesian Ranking

### Model Summary
This project implements a proof-of-concept pipeline that translates multimodal perturbation phenotypes into ranked cardiometabolic disease (CMD) target candidates.

It combines:
- joint embedding representation learning (morphology + transcriptomics)
- supervised CMD relevance prediction
- probability calibration
- Bayesian multi-evidence integration to rank gene targets with uncertainty awareness

Primary output:
- `ranked_targets_bayesian.csv`

## Intended Use
**Primary users**
- computational biologists
- target discovery scientists
- BioAI researchers

**Primary use cases**
- hypothesis generation for CMD target discovery
- compound→gene mechanism-style reinforcement in phenotype space
- ranking and prioritization with uncertainty estimates
- benchmarking and evaluation of multimodal representation methods

**Not intended for**
- clinical use
- final therapeutic claims without experimental validation

## Inputs and Outputs

### Inputs
Condition-level profiles built from replicate measurements:
- morphology profile (512D)
- transcriptomic signature profile (256D)
- metadata including perturbation type, batch, context

### Outputs
- Bayesian-ranked targets: `ranked_targets_bayesian.csv`
- Integrated score targets: `ranked_targets.csv`
- Compound→gene links: `compound_gene_links.csv`
- QC metrics: `qc_replicate_consistency.csv`

## Core Components

### 1) Multimodal representation (joint embedding)
The pipeline creates a single “phenotype space” by:
- PCA reduction of morphology profiles
- PCA reduction of transcriptomics profiles
- feature standardization
- concatenation into a joint embedding Z

This baseline can be replaced by deep multimodal fusion in production.

### 2) CMD relevance classifier
A Random Forest classifier estimates CMD target-likeness for gene perturbations:
- trained on gene perturbation embeddings
- evaluated using ROC-AUC and PR-AUC
- probabilities calibrated for interpretability

### 3) Compound→gene phenotype matching
Compounds are linked to genes by embedding similarity.
Strong compound reinforcement increases target confidence.

### 4) Bayesian evidence integration
The final prioritization integrates multiple evidence channels:
- calibrated ML probability
- phenotype strength (distance from control centroid)
- cross-modal concordance (morph ↔ tx agreement after alignment)
- compound support (strong links from compounds)
- phenotype graph centrality (inbound degree proxy)

Outputs include:
- posterior mean confidence
- posterior variance uncertainty

## Evaluation
Classifier evaluation metrics:
- ROC-AUC
- PR-AUC

Recommended ranking evaluation for real datasets:
- enrichment at top-k
- precision/recall of compound→gene links (when MoA labels exist)
- cluster coherence metrics

## Limitations
- PoC uses simulated data, real-world distributions differ
- fusion is linear and may miss non-linear cross-modal structure
- similarity-based linking does not prove causality
- lightweight batch correction intended for transparency
- no direct integration of human genetics evidence yet

## Responsible Use
This system produces ranked hypotheses.
All targets require orthogonal validation before any downstream claims.

# Dataset Card
## CMD Multimodal Perturbation Screen Simulation (PoC)

### Dataset Summary
This PoC generates a simulated multimodal perturbation screening dataset designed to resemble real target discovery screens.

It includes:
- gene perturbations (genome-scale candidates)
- compound perturbations (small molecules)
- negative controls (DMSO / non-targeting)
- batch effects, replicates, and multi-context structure
- two paired modalities:
  - morphology embeddings (512D)
  - transcriptomics signatures (256D)

Generated by:
- `cmd_target_discovery_poc.py`

## Motivation
This dataset exists to demonstrate and benchmark pipelines that:
- QC replicate consistency across modalities
- build joint multimodal phenotype embeddings
- link compounds to genes via phenotype similarity
- rank targets using integrated confidence and uncertainty

## Files Produced

### 1) Replicate-level metadata
`meta_raw.csv`

Fields include:
- entity_type (gene / compound / control)
- entity_id
- module (latent module index)
- batch
- cell_line/context
- replicate
- potency
- is_cmd_true_target (genes only)

### 2) QC outputs
`qc_replicate_consistency.csv`
- morph reproducibility (replicate-to-mean cosine similarity)
- tx reproducibility (replicate-to-mean cosine similarity)

### 3) Compound→gene links
`compound_gene_links.csv`
- top gene neighbors per compound using embedding similarity

### 4) Ranked targets
`ranked_targets.csv`
- integrated heuristic evidence score

`ranked_targets_bayesian.csv`
- Bayesian posterior mean confidence and posterior variance uncertainty

## Modalities

### Morphology embeddings
- dimension: 512
- represents Cell Painting-like morphological phenotype embeddings
- includes batch effects and measurement noise

### Transcriptomics signatures
- dimension: 256
- represents DRUG-seq / Perturb-seq-like perturbational gene expression signatures
- includes batch effects and measurement noise

## Simulation Design Assumptions
- latent pathway modules drive phenotype shifts
- CMD-driving genes are enriched for strong module effects
- compounds can be module-linked or polypharmacologic mixtures
- controls exhibit minimal phenotype shift

## Limitations
- simulated distributions do not replicate all real-world structure
- no dose-response or time-series profiles
- no segmentation artifacts for imaging
- no named biological pathways (modules are abstract)

## Replacing with real screening data
To adapt this repository to real perturbation profiling:
- replace simulator with real embeddings:
  - Cell Painting morphology profiles
  - DRUG-seq, sci-Plex, or Perturb-seq signatures
- provide metadata columns:
  - entity_type, entity_id, batch, replicate, context
- run QC → embedding → linking → Bayesian ranking without changing core logic

## Software / code repository citation

**Petalcorin, M. I. R.** (2026). CMD Target Discovery PoC: Multimodal perturbation screens to high-confidence cardiometabolic disease drug target candidates (Version 1.0.0) [Computer software]. GitHub. https://https://github.com/mpetalcorin/CMD-Target-Discovery-PoC-Multimodal-Perturbation-Screens

## References 
Bray, M.-A., Singh, S., Han, H., Davis, C. T., Borgeson, B., Hartland, C., Kost-Alimova, M., Gustafsdottir, S. M., Gibson, C. C., & Carpenter, A. E. (2016). Cell Painting, a high-content image-based assay for morphological profiling using multiplexed fluorescent dyes. *Nature Protocols, 11*(9), 1757–1774. https://doi.org/10.1038/nprot.2016.105

Dixit, A., Parnas, O., Li, B., Chen, J., Fulco, C. P., Jerby-Arnon, L., et al. (2016). Perturb-seq: Dissecting molecular circuits with scalable single-cell RNA profiling of pooled genetic screens. *Cell, 167*(7), 1853–1866.e17. https://doi.org/10.1016/j.cell.2016.11.038

Subramanian, A., Narayan, R., Corsello, S. M., Peck, D. D., Natoli, T. E., Lu, X., et al. (2017). A next generation connectivity map: L1000 platform and the first 1,000,000 profiles. *Cell, 171*(6), 1437–1452.e17. https://doi.org/10.1016/j.cell.2017.10.049

Way, G. P., Natoli, T., Adeboye, A., Litichevskiy, L., Yang, A., Lu, X., et al. (2022). Morphology and gene expression profiling provide complementary information for mapping cell state. *Cell Systems, 13*(11), 911–923.e9. https://doi.org/10.1016/j.cels.2022.10.001
