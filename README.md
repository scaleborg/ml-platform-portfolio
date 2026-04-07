# ML Platform for Real-Time Decisioning

Production-grade ML platform designed for real-time decisioning systems.

This repository is the entry point to a multi-repo system that covers the full lifecycle of machine learning in production: from data ingestion to feature computation, model training, and low-latency serving.

---

## System Overview

The platform is structured as a set of interoperable layers:

P1 → P2 → P3 → P4 → P5

| Layer | Component | Responsibility |
|------|----------|----------------|
| P1 | urban-mobility-control-tower | Real-time data ingestion, CDC, streaming aggregation, analytical surface |
| P2 | mobility-feature-pipeline | Point-in-time feature engineering and dataset generation |
| P3 | mobility-feature-store | Feature storage, point-in-time retrieval, offline/online consistency |
| P4 | ml-training-orchestrator | Reproducible training, evaluation, and model packaging |
| P5 | mobility-serving-layer | Low-latency inference and API serving |

Each repository represents a system boundary with explicit contracts between layers.

---

## Architecture

End-to-end flow:

1. **Ingestion (P1)**  
   GBFS → Postgres → Debezium CDC → Kafka → Flink → DuckDB  

2. **Feature computation (P2)**  
   SQL-based feature engineering with strict temporal constraints  

3. **Feature storage (P3)**  
   Parquet datasets + point-in-time retrieval (ASOF JOIN semantics)  

4. **Model training (P4)**  
   Time-based splits, experiment tracking, model packaging  

5. **Serving (P5)**  
   Real-time feature loading + model inference via API  

---

## System properties

- Strict separation of concerns across layers  
- Deterministic feature contracts between training and serving  
- No feature recomputation outside the feature pipeline  
- Point-in-time correctness across the entire system  
- Reproducible training datasets  
- Stateless, horizontally scalable serving layer  

---

## Design approach

This platform is built as a system, not a collection of projects.

Each layer is:

- independently deployable  
- contract-driven  
- composable with the rest of the stack  

The focus is not only model performance, but system reliability, reproducibility, and correctness under real-world constraints.

---

## Current implementation

This system is currently demonstrated through an urban mobility use case (bike station availability prediction), but the architecture is designed to be reusable across domains.

---

## Repository links

- P1 — https://github.com/scaleborg/urban-mobility-control-tower  
- P2 — https://github.com/scaleborg/mobility-feature-pipeline  
- P3 — https://github.com/scaleborg/mobility-feature-store  
- P4 — https://github.com/scaleborg/ml-training-orchestrator  
- P5 — https://github.com/scaleborg/mobility-serving-layer  
