# 🌟 NorthStar Retail — AI Customer Intelligence Platform

> An **end-to-end AI lakehouse** built on **Azure Databricks** — spanning data engineering, machine learning, and generative AI. From raw e-commerce files to a tool-using AI agent, all governed, orchestrated, and production-ready.

![Platform](https://img.shields.io/badge/Platform-Azure%20Databricks-FF3621?logo=databricks&logoColor=white)
![Unity Catalog](https://img.shields.io/badge/Governance-Unity%20Catalog-1B3139)
![Delta Lake](https://img.shields.io/badge/Storage-Delta%20Lake-00ADD4)
![MLflow](https://img.shields.io/badge/MLOps-MLflow-0194E2?logo=mlflow&logoColor=white)
![GenAI](https://img.shields.io/badge/GenAI-RAG%20%7C%20Agents%20%7C%20Vector%20Search-6f42c1)
![License](https://img.shields.io/badge/License-MIT-green)

---

## 📌 Overview

This project transforms the **[Olist Brazilian E-Commerce dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)** (~100K orders, ~99K customers, ~40K free-text reviews) into a complete, AI-powered analytics platform.

It answers three questions a real business cares about:

1. **What happened?** → Data engineering (Medallion architecture)
2. **What will happen?** → Machine learning (late-delivery prediction)
3. **What are customers saying?** → Generative AI (sentiment, search, chatbot, agents)

Built over 6 phases, the platform demonstrates the **full modern data + AI lifecycle** on a single governed lakehouse.

---

## 🏗️ Architecture

```
                          ┌─────────────────────────────────────────────┐
                          │              UNITY CATALOG (Governance)       │
                          └─────────────────────────────────────────────┘
   Olist CSVs                     │              │              │
   (cloud storage)                ▼              ▼              ▼
        │              ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
        │  Auto Loader │   BRONZE     │  │   SILVER     │  │    GOLD      │
        └─────────────▶│  raw, as-is  │─▶│ clean, typed │─▶│ star schema  │
                       │  (9 tables)  │  │  deduped     │  │ facts + dims │
                       └──────────────┘  └──────────────┘  └──────┬───────┘
                                                                  │
                       ┌──────────────────────────────────────────┼──────────────────────┐
                       ▼                                          ▼                        ▼
              ┌─────────────────┐                      ┌──────────────────┐     ┌──────────────────┐
              │   CLASSIC ML    │                      │     GEN AI       │     │   ORCHESTRATION  │
              │ late-delivery   │                      │ AI Functions     │     │ DLT pipelines    │
              │ prediction      │                      │ Vector Search    │     │ Workflows (Jobs) │
              │ MLflow + serving│                      │ RAG + Agents     │     │ scheduled+retry  │
              └─────────────────┘                      └──────────────────┘     └──────────────────┘
```

---

## 🚀 The 6 Phases

### Phase 1 — Data Foundation (Medallion Architecture)
- **Bronze:** Incremental ingestion of 9 Olist tables via **Auto Loader** (schema evolution, exactly-once, idempotent).
- **Silver:** Cleansing, type-casting, **deduplication with window functions**, null handling, multi-line CSV parsing, quality checks.
- **Gold:** **Kimball star schema** — 4 dimensions (`dim_customer`, `dim_product`, `dim_seller`, `dim_date`), 2 facts (`fct_orders`, `fct_order_items`), and 4 pre-aggregated tables (`customer_360`, `sales_by_category`, `daily_revenue`, `seller_performance`).

### Phase 2 — Declarative Pipelines & Orchestration
- **Delta Live Tables (Lakeflow)** pipeline rebuilding Bronze→Silver→Gold *declaratively* with `@dlt.expect` **data-quality constraints** and automatic lineage.
- **Workflows (Jobs)** orchestrating the pipeline on a schedule, with task dependencies, retries, and failure alerts.

### Phase 3 — Classic ML (Late-Delivery Prediction)
- **Leakage-free feature engineering** from the Gold layer.
- **Random Forest classifier** trained with **MLflow** experiment tracking, handling **class imbalance** (`class_weight="balanced"`).
- **Results:** `AUC = 0.758`, **recall (late) = 0.56** — catches over half of all late orders.
- Registered in the **Unity Catalog Model Registry** (`champion` alias) and deployed to a **scale-to-zero serving endpoint** for real-time predictions.

### Phase 4 — Generative AI on Customer Reviews
| Task | Built |
|------|-------|
| **B1 — AI Functions** | `ai_analyze_sentiment`, `ai_classify`, `ai_summarize` on **40,668 Portuguese reviews** → a Gold insights table |
| **B2 — Vector Search** | Semantic search with **multilingual embeddings** (Qwen3) — English queries find Portuguese reviews by *meaning* |
| **B3 — RAG Chatbot** | Retrieval-Augmented Generation — grounded, hallucination-free answers over the reviews |
| **B4 — AI Agent** | A tool-using agent that **chooses** between unstructured (reviews) and structured (SQL) tools |
| **B5 — Evaluation** | **LLM-as-a-judge** scoring answer relevance & groundedness |
| **B6 — Genie** | Natural-language BI — ask the Gold tables questions in plain English |

### Phase 5 — Performance & Cost Optimization
- Delta `OPTIMIZE`, **Liquid Clustering**, `VACUUM`, Spark UI query-profile analysis, **Photon**.
- **FinOps discipline:** sample-before-scale, batch over real-time, scale-to-zero serving, deleting standing resources.

### Phase 6 — CI/CD
- **Git integration** (this repo) for version control.
- **Databricks Asset Bundles** to deploy the entire platform (pipelines, jobs, models) across **dev → prod** as infrastructure-as-code.

---

## 🔑 Key Business Insights

> The platform's three layers all point at the **same truth** — making the case for action.

- 📦 **Delivery is the #1 customer pain point** — ~**6,925 negative delivery reviews** (the largest complaint category, ~36% of all delivery mentions).
- ⏱️ **8.1% of orders are delivered late** — quantified directly from the Gold fact table.
- 🤖 The **ML model predicts** late deliveries, **GenAI confirms** customers complain about them, and the **agent** answers questions about both — a fully coherent data-to-insight loop.

---

## 🛠️ Tech Stack

| Layer | Technologies |
|-------|-------------|
| **Platform** | Azure Databricks, Unity Catalog, Delta Lake |
| **Ingestion** | Auto Loader (`cloudFiles`), Volumes, External Locations |
| **Transformation** | PySpark, Spark SQL, Delta Live Tables (Lakeflow) |
| **Orchestration** | Databricks Workflows (Jobs) |
| **Machine Learning** | MLflow, scikit-learn, Model Serving |
| **Generative AI** | Mosaic AI (Foundation Models, Vector Search, Agent Framework), AI Functions, MCP |
| **DevOps** | Git, Databricks Asset Bundles |

---

## 📁 Repository Structure

```
.
├── phase0_setup/          # Unity Catalog, storage, external locations
├── phase1_data/           # Bronze, Silver, Gold notebooks
├── phase2_pipelines/      # DLT pipeline + Workflow
├── phase3_ml/             # Feature engineering, training, serving
├── phase4_genai/          # AI Functions, Vector Search, RAG, Agent, Eval
├── phase5_performance/    # OPTIMIZE, clustering, tuning
├── databricks.yml         # Asset Bundle (Phase 6)
└── README.md
```

---

## 🧠 Skills Demonstrated

- **Data Engineering:** Medallion architecture, incremental ingestion, SCD/dedup patterns, dimensional modeling
- **Data Governance:** Unity Catalog, managed vs. external tables, lineage, access control
- **MLOps:** Feature engineering, experiment tracking, model registry, real-time serving, handling class imbalance
- **GenAI / LLMOps:** Batch LLM inference, embeddings, semantic search, RAG, agentic AI, LLM-as-a-judge evaluation
- **DataOps:** Declarative pipelines, orchestration, performance tuning, FinOps, CI/CD

---

## 📊 Dataset

[Olist Brazilian E-Commerce Public Dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) — ~100K real, anonymized orders (2016–2018) across 9 relational tables, including free-text customer reviews in Portuguese.

---

## 📜 License

MIT — see [LICENSE](LICENSE).

---

<p align="center"><i>Built end-to-end on Azure Databricks — data engineering → ML → GenAI, all on one governed lakehouse.</i></p>
