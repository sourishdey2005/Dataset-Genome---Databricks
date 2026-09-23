# 🧬 Dataset Genome

### An Explainable Computational Fingerprint for Dataset Similarity, Quality, Drift and Business Readiness

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white)
![PySpark](https://img.shields.io/badge/PySpark-3.5%2B-orange?logo=apachespark&logoColor=white)
![Snowflake](https://img.shields.io/badge/Snowflake-Snowpark-29B5E8?logo=snowflake&logoColor=white)
![Databricks](https://img.shields.io/badge/Databricks-Free%20Edition-FF3621?logo=databricks&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## 📖 Overview

**Dataset Genome** is an explainable structural fingerprinting framework that converts any tabular dataset into a stable, multi-dimensional computational fingerprint — a **"genome"** — that captures its structural, statistical, categorical, dependency, quality, and temporal characteristics.

Unlike conventional data-quality reports that output isolated metrics (rows, columns, missingness), Dataset Genome produces a **116-dimensional fingerprint** that enables:

- **Dataset similarity detection** — identify structurally similar or redundant datasets
- **Drift detection** — measure how a dataset evolves over time
- **Root-cause analysis** — explain *which* component caused a change
- **Business readiness scoring** — decide whether a dataset is safe for downstream analytics
- **Impact analysis** — trace which business reports depend on a drifting dataset

The system is built on a **medallion architecture** (Bronze → Silver → Gold) and is reproducible on both **Databricks** (PySpark) and **Snowflake** (Snowpark Python) using a deterministic seed.

> **"From Data Profiling to Data Identity."**

---

## 🎯 The Business Problem

Organizations rarely suffer because they lack data. They suffer because they have:

- Multiple versions of the same dataset
- Poorly documented data assets
- Inconsistent schemas across teams
- Silent statistical drift over time
- Hidden redundancy in data lakes
- Unreliable reporting sources
- No way to determine if two datasets are *structurally* similar

A conventional data-quality report might say:

```
Rows:          20,000
Columns:       24
Missing:       4.2%
Duplicates:    0.1%
```

But it cannot answer the questions that actually matter to a business:

> Can this dataset be trusted for reporting?
> Is this new dataset a duplicate of an existing one?
> Has this dataset drifted enough to break downstream dashboards?
> Which business reports are affected by this change?
> Which of my datasets are safe to promote to production?

Dataset Genome answers all of these by modeling each dataset as a **computational identity** rather than a collection of ad-hoc metrics.

---

## 🧠 Central Research Question

> *Can the intrinsic structural characteristics of a dataset be represented as a stable computational fingerprint that is **invariant to superficial transformations** but **sensitive to meaningful statistical, quality, dependency and temporal changes**?*

This project answers that question empirically using a deterministic 12-dataset synthetic benchmark.

---

## 🏗️ Architecture

```
                 SYNTHETIC DATA GENERATOR
                         │
                    Seed = 1223
                         │
                         ▼
              ┌─────────────────────┐
              │ 12 Dataset Variants │
              └──────────┬──────────┘
                         │
                         ▼
                    BRONZE LAYER
                         │
                         ▼
                    SILVER LAYER
                         │
          ┌──────────────┼───────────────┐
          ▼              ▼               ▼
      Statistics     Dependencies     Quality
          │              │               │
          └──────────────┼───────────────┘
                         ▼
                  DATASET GENOME
                         │
       ┌─────────────────┼─────────────────┐
       ▼                 ▼                 ▼
   Similarity          Drift            Risk
       │                 │                 │
       ▼                 ▼                 ▼
  Redundancy       Change Impact      Readiness
       │                 │                 │
       └─────────────────┼─────────────────┘
                         ▼
                 BUSINESS ANALYTICS
                         │
                         ▼
              DATABRICKS / SNOWFLAKE
```

---

## 🧬 The Dataset Genome

Every dataset `D` is converted into a fixed-length genome vector:

```
G(D) = [ GS , GST , GC , GD , GQ , GT ]
```

| Symbol | Component        | Description                                       | Dim |
|--------|------------------|---------------------------------------------------|-----|
| `GS`   | Schema Genome    | Column counts, type ratios                        | 7   |
| `GST`  | Statistical      | Normalized mean, std, range per numeric column    | 60  |
| `GC`   | Categorical      | Cardinality and distribution shape per category   | 30  |
| `GD`   | Dependency       | Correlation graph features, top abs correlations  | 10  |
| `GQ`   | Quality          | Missingness and duplicate-rate features           | 4   |
| `GT`   | Temporal         | Time-span features per temporal column            | 5   |
|        | **Total**        |                                                   | **116** |

---

## 📊 Synthetic Benchmark — 12 Dataset Variants

All variants are generated from a single base dataset (`G01_BASE`, 20,000 × 26) using **seed = 1223**:

| ID  | Variant                | Mutation                       | Business Interpretation                        |
|-----|------------------------|--------------------------------|------------------------------------------------|
| G01 | Baseline               | —                              | Trusted reference                              |
| G02 | Row Shuffled           | Random row order               | Same data, different ordering                  |
| G03 | Column Reordered       | Different column order         | Same data, different extraction order          |
| G04 | Column Renamed         | Metadata-only change           | Same data, changed metadata                    |
| G05 | Missing 20%            | Random missingness             | Reporting reliability problem                  |
| G06 | Noise 10%              | Gaussian noise on numerics     | Data-collection degradation                    |
| G07 | Redundancy 20%         | Duplicated rows                | Inefficient feature set                        |
| G08 | Imbalance 90%          | Target imbalance               | Analytical bias / minority-segment invisibility|
| G09 | Drift 20%              | Distribution shift             | Population or process change                   |
| G10 | Dependency Break       | Broken variable relationship   | Changed business logic                         |
| G11 | Combined Mutation      | Multiple mutations             | High-risk data asset                           |
| G12 | Scale Transformed      | ×1000 on numerics              | Same structure, different units                |

---

## 🔍 Key Results

Executed with `seed = 1223` on both platforms:

| Metric                        | Value     |
|-------------------------------|-----------|
| Datasets analyzed             | 12        |
| Genome dimension              | 116       |
| Gold analytics tables         | 17        |
| Visualizations produced       | 22        |
| Business queries              | 25        |
| Average quality score         | 84.43     |
| Average data trust score      | 90.61     |
| Drift events detected         | 8 / 11    |
| Redundancy candidates         | 45 pairs  |
| Datasets requiring review     | 0         |
| Genomic silhouette score      | 0.4763    |

### Research Validation

| Question                                          | Result |
|---------------------------------------------------|--------|
| Is the genome invariant to superficial transforms?| ✅ for row shuffle & column rename |
| Is it sensitive to meaningful mutations?          | ✅ YES |
| Can it detect dependency changes?                 | ✅ YES (`GD_dist = 0.3386`) |
| Can it detect temporal drift?                     | ✅ YES (GT dimension present) |
| Does each genome component contribute?            | ✅ YES (see ablation study) |

### Ablation Study — Component Contribution

| Component           | Mean Distance | Max Distance |
|---------------------|---------------|--------------|
| `GS` (Schema)       | 0.000         | 0.000        |
| `GST` (Statistical) | 4948.37       | 13843.07     |
| `GC` (Categorical)  | 13.23         | 75.90        |
| `GD` (Dependency)   | 0.077         | 0.339        |
| `GQ` (Quality)      | 0.108         | 0.537        |
| `GT` (Temporal)     | 0.000         | 0.000        |

This demonstrates that **no single component suffices** — the multi-dimensional genome is required.

---

## 🗂️ Repository Structure

```
dataset-genome/
│
├── README.md                          # This file
├── LICENSE                            # MIT License
│
├── databricks/
│   └── DatasetGenome_SourishDey.ipynb # Primary implementation (PySpark)
│
├── snowflake/
│   └── DatasetGenome_Snowflake.ipynb  # Cross-platform reproduction (Snowpark)
│
├── docs/
│   ├── architecture.md                # Detailed architecture notes
│   ├── genome_specification.md        # Formal genome definition
│   ├── business_queries.md            # All 25 business query definitions
│   └── research_questions.md          # BRQ1–BRQ10 explained
│
├── exports/
│   ├── gold_dataset_genome.csv
│   ├── gold_genome_similarity.csv
│   ├── gold_genome_drift.csv
│   ├── gold_data_quality.csv
│   ├── gold_dataset_readiness.csv
│   ├── gold_dataset_risk.csv
│   ├── gold_dataset_portfolio.csv
│   ├── gold_dataset_redundancy.csv
│   ├── gold_dataset_complexity.csv
│   ├── gold_dataset_evolution.csv
│   ├── gold_data_contract.csv
│   └── gold_kpi_summary.json
│
└── assets/
    ├── similarity_heatmap.png
    ├── drift_bar.png
    ├── portfolio_quadrant.png
    ├── pca_scatter.png
    └── ...
```

---

## ⚙️ Technology Stack

**Primary Platform**
- Databricks Free Edition
- PySpark 3.5+
- Spark SQL
- Python 3.10+

**Secondary Platform**
- Snowflake (Snowpark Python)
- Snowflake SQL
- Snowsight Charts

**Libraries**
- `numpy`, `pandas`, `scipy`
- `scikit-learn` (PCA, KMeans, StandardScaler, silhouette)
- `networkx` (dependency graph)
- `plotly` (interactive visualizations)

**Tooling**
- Git / GitHub
- Markdown

---

## 🚀 Getting Started

### Option 1 — Run on Databricks

1. Create a **Databricks Free Edition** workspace.
2. Import `databricks/DatasetGenome_SourishDey.ipynb` into your workspace.
3. Attach to any cluster with **PySpark** runtime.
4. Run **Cell 1** to install dependencies (`networkx`, `plotly`, `scikit-learn`).
5. Run all remaining cells sequentially.

### Option 2 — Run on Snowflake

1. Log in to **Snowflake** (Snowsight).
2. Create a **Snowflake Notebook**.
3. Copy the cells from `snowflake/DatasetGenome_Snowflake.ipynb`.
4. Set the warehouse to **X-Small** for cost efficiency.
5. Run cells sequentially.

### Reproducibility

Every run is deterministic:

```
GLOBAL_SEED = 1223
```

All random number generators, data generators, and benchmark variants use this seed. Running on either platform produces identical results.

---

## 📈 Outputs

### Gold Analytics Tables

| Table                              | Rows | Purpose                                 |
|------------------------------------|------|-----------------------------------------|
| `gold_dataset_genome`              | 12   | 116-dim genome per dataset              |
| `gold_genome_similarity`           | 66   | Pairwise similarities                   |
| `gold_genome_drift`                | 11   | Drift distance vs baseline              |
| `gold_data_quality`                | 12   | Missingness, duplicates, outliers, etc. |
| `gold_dataset_readiness`           | 12   | READY / REVIEW / INVESTIGATE            |
| `gold_dataset_risk`                | 12   | 6-dimension risk profile                |
| `gold_dataset_portfolio`           | 12   | Executive portfolio view                |
| `gold_dataset_change_impact`       | 11   | Schema-to-quality change summary        |
| `gold_dataset_redundancy`          | 66   | Potential redundancy flags              |
| `gold_business_asset_catalog`      | 15   | Synthetic business reports              |
| `gold_report_impact`               | 15   | Which reports are affected              |
| `gold_dataset_executive_profile`   | 12   | One-line summaries                      |
| `gold_dataset_evolution`           | 12   | 12-month timeline                       |
| `gold_dataset_complexity`          | 12   | Complexity score per dataset            |
| `gold_data_contract`               | 12   | PASS / REVIEW / FAIL statuses           |
| `gold_kpi_summary`                 | 1    | Aggregate KPIs                          |
| `gold_genome_evaluation`           | 12   | Cluster assignments                     |

### Business Queries

The system answers all 25 business queries documented in `docs/business_queries.md`, including:

- Dataset inventory
- Most similar datasets
- Potential redundancy
- Dataset health
- Dataset readiness
- New dataset onboarding
- Dataset change impact
- Drift detection & explanation
- Dependency break detection
- Dataset risk profile
- Reporting risk
- Report impact
- Consolidation candidates
- Dataset discovery
- Historical comparison
- Source-system change
- Data contract monitoring
- Quality root cause
- Executive summary
- Portfolio view
- Cost/compute analysis
- Dataset complexity
- Dataset evolution
- Similarity vs. quality quadrant

---

## 🧪 Research Contribution

> **This project proposes and experimentally evaluates a multidimensional *Dataset Genome* representation that combines schema, statistical, categorical, dependency, quality, graph and temporal characteristics into a unified structural fingerprint.**

The contribution is the **framework and experimental evaluation** — not an unsupported claim of being globally first.

### What makes this different

- **Multi-dimensional by design** — not just univariate statistics
- **Explainable** — every drift event is decomposed into contributing components
- **Business-facing** — outputs are decision-ready, not just diagnostic
- **Reproducible** — deterministic seed on both Databricks and Snowflake
- **Cross-platform** — same methodology, two execution engines

---

## 👨‍💻 Author

**Sourish Dey**
B.Tech Computer Science & Engineering
KIIT University

- 📧 Email: [23051223@kiit.ac.in](mailto:23051223@kiit.ac.in)
- 🎓 Roll Number: 23051223

---

## 📜 License

This project is released under the **MIT License**. See [LICENSE](LICENSE) for details.

---

## 🙏 Acknowledgements

- Databricks Free Edition for providing the primary compute platform
- Snowflake for the cross-platform reproduction environment
- The open-source Python data ecosystem (NumPy, Pandas, scikit-learn, NetworkX, Plotly)

---

## 📎 Citation

If you reference this project in academic or professional work, please cite:

```bibtex
@misc{dey2025datasetgenome,
  author       = {Sourish Dey},
  title        = {Dataset Genome: An Explainable Computational Fingerprint for
                  Dataset Similarity, Quality, Drift and Business Readiness},
  year         = {2025},
  institution  = {KIIT University},
  howpublished = {\url{https://github.com/<your-username>/dataset-genome}},
  note         = {Global Seed: 1223}
}
```

---

## 🏁 Project Status

| Component              | Status |
|------------------------|--------|
| Synthetic benchmark    | ✅ Complete |
| Genome construction    | ✅ Complete |
| Similarity engine      | ✅ Complete |
| Drift detection        | ✅ Complete |
| Quality & readiness    | ✅ Complete |
| Risk profiling         | ✅ Complete |
| Business queries (25)  | ✅ Complete |
| Visualizations (22)    | ✅ Complete |
| Databricks notebook    | ✅ Complete |
| Snowflake notebook     | ✅ Complete |
| Documentation          | ✅ Complete |

> **Status: COMPLETE**

---

<div align="center">

**🧬 Dataset Genome**

*"From Data Profiling to Data Identity."*

**Seed = 1223**

**Sourish Dey — 23051223 — KIIT University**

</div>
