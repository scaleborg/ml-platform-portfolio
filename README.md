# ML Platform for Real-Time Decisioning

Production-grade ML platform designed for real-time decisioning systems.

This repository is the entry point to a multi-repo system that covers the full lifecycle of machine learning in production: from real-time data ingestion to feature computation, feature serving, model training, low-latency inference, and operational monitoring.

## System overview

The platform is structured as a set of interoperable layers:

**P1 → P2 → P3 → P4 → P5 → P6**

P6 closes the loop by making the system observable after deployment: dataset lineage, model lineage, freshness, runtime metrics, validation signals, and future retraining triggers.

| Layer | Component | Responsibility |
|-------|-----------|----------------|
| P1 | `urban-mobility-control-tower` | Real-time data ingestion, CDC, streaming aggregation, analytical surface |
| P2 | `mobility-feature-pipeline` | Point-in-time feature engineering and supervised dataset generation |
| P3 | `mobility-feature-store` | Feature storage, point-in-time retrieval, offline/online consistency |
| P4 | `ml-training-orchestrator` | Reproducible training, evaluation, experiment tracking, model packaging |
| P5 | `mobility-serving-layer` | Real-time inference, deterministic feature reconstruction, deployment and metrics observability |
| P6 | `monitoring-feedback-layer` | Per-deployment health classification, lineage, freshness, gap and staleness detection |

Each repository represents a system boundary with explicit contracts between layers.

## Architecture

End-to-end system flow:

### Ingestion (P1)
GBFS → Postgres → Debezium CDC → Kafka → Flink → DuckDB

### Feature computation (P2)
SQL-based feature engineering with strict temporal constraints and point-in-time correctness.

### Feature storage (P3)
Parquet datasets, offline/online retrieval paths, and point-in-time lookup semantics.

### Model training (P4)
Time-based splits, reproducible training runs, evaluation, experiment tracking, and model packaging.

### Serving (P5)
Real-time inference serving from upstream platform data. Features are reconstructed point-in-time from the same source used in training, with strict schema-driven parity enforcement. Emits structured deployment events and 60-second metrics windows as append-only artifacts for downstream monitoring.

### Monitoring and feedback (P6)
Consumes real P5 serving artifacts and computes deterministic per-deployment health classification across three states (healthy, degraded, unhealthy). Health evaluation accounts for staleness, window gaps, missing windows, latency, and error rates. Maintains dataset-to-model lineage, freshness tracking, and validation signals that close the operational loop.

## Completion status

The P1 → P5 → P6 operational loop has been validated end-to-end on real artifacts. Serving metrics flow from P5 into P6, where per-deployment health is computed deterministically against configurable thresholds. Lineage traces datasets through training to serving. Dashboards, alerting infrastructure, and automated retraining triggers are intentionally deferred as separate concerns outside this platform slice.

## System properties

- Strict separation of concerns across layers
- Explicit contracts between ingestion, features, training, serving, and monitoring
- Deterministic feature definitions between training and serving
- No feature recomputation outside the feature pipeline
- Point-in-time correctness across the full system
- Reproducible training datasets and model runs
- Stateless, horizontally scalable serving layer
- Operational observability tied to actual ML artifacts and contracts
- Clear path toward closed-loop retraining

## Design approach

This platform is built as a system, not a collection of projects.

Each layer is:

- independently understandable
- contract-driven
- composable with the rest of the stack
- scoped to a single operational responsibility

The objective is not only model performance. The system is designed around reliability, reproducibility, temporal correctness, and production-grade interfaces between layers.

## Current implementation

The current implementation is demonstrated through an urban mobility use case focused on bike station availability prediction.

The architectural pattern is platform-level and reusable beyond mobility. The same layer model can be extended to other operational decisioning domains such as retail, logistics, fulfillment, and marketplace systems.

## Repository links

- **P1** — `urban-mobility-control-tower`  
  https://github.com/scaleborg/urban-mobility-control-tower

- **P2** — `mobility-feature-pipeline`  
  https://github.com/scaleborg/mobility-feature-pipeline

- **P3** — `mobility-feature-store`  
  https://github.com/scaleborg/mobility-feature-store

- **P4** — `ml-training-orchestrator`  
  https://github.com/scaleborg/ml-training-orchestrator

- **P5** — `mobility-serving-layer`  
  https://github.com/scaleborg/mobility-serving-layer

- **P6** — `monitoring-feedback-layer`  
  https://github.com/scaleborg/monitoring-feedback-layer
