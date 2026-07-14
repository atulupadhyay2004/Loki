# 📊 Full Cluster Logging with Loki Stack

[![GitHub repo size](https://img.shields.io/github/repo-size/atulupadhyay2004/Loki)](https://github.com/atulupadhyay2004/Loki)
[![GitHub stars](https://img.shields.io/github/stars/atulupadhyay2004/Loki?style=social)](https://github.com/atulupadhyay2004/Loki/stargazers)

> A complete Kubernetes logging pipeline built from scratch on Minikube using the **Loki Stack** — Promtail, Loki, and Grafana. Lightweight, cost-effective, and designed for cloud-native environments.

---

## 📋 Table of Contents
- [About The Project](#-about-the-project)
- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [System Architecture](#-system-architecture)
- [Workflow](#-workflow)
- [LogQL Queries Mastered](#-logql-queries-mastered)
- [Dashboards Built](#-dashboards-built)
- [Getting Started](#-getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Deploy the Error Generator](#deploy-the-error-generator)
  - [Access Grafana](#access-grafana)
- [Key Learning](#-key-learning)
- [Contributing](#-contributing)
- [License](#-license)
- [Contact](#-contact)

---

## 📖 About The Project

This project sets up a **production-ready logging pipeline** for Kubernetes clusters. It includes:

- **Promtail** — DaemonSet that collects logs from every pod and forwards them to Loki
- **Loki** — Horizontally scalable, highly available log aggregation system that indexes only labels (not log content)
- **Grafana** — Powerful visualization tool for querying and exploring logs using LogQL

The stack is deployed on **Minikube** and includes a sample **error generator** application that produces realistic log entries (INFO, WARNING, ERROR, CRITICAL) to demonstrate the logging pipeline in action.[reference:0][reference:1]

---

## ✨ Features

- ✅ **Complete logging pipeline** — Promtail → Loki → Grafana
- ✅ **Lightweight architecture** — Loki indexes labels only, not full log content[reference:2]
- ✅ **LogQL mastery** — Filter by namespace, app, log content, count rates, parse JSON, extract fields[reference:3]
- ✅ **Interactive dashboards** — All logs, error logs, log rate, error rate, logs per app, namespace + app filters[reference:4]
- ✅ **Error generator** — Demo app producing realistic log patterns for testing[reference:5]
- ✅ **Kubernetes-native** — Deployed entirely on Minikube with Helm

---

## 🛠 Tech Stack

| Category | Technology |
|----------|------------|
| **Log Collector** | Promtail (DaemonSet) |
| **Log Storage** | Loki (Horizontally scalable) |
| **Visualization** | Grafana |
| **Orchestration** | Kubernetes (Minikube) |
| **Package Manager** | Helm |
| **Demo App** | Busybox (error-generator) |

---

## 🧠 System Architecture

Below is the high-level architecture of the Loki logging stack:
┌─────────────────────────────────────────────────────────────────────────────────┐
│ KUBERNETES CLUSTER (Minikube) │
│ │
│ ┌─────────────────────────────────────────────────────────────────────────┐ │
│ │ NAMESPACE: demo-apps │ │
│ │ │ │
│ │ ┌─────────────────────────────────────────────────────────────────┐ │ │
│ │ │ error-generator (Pod) │ │ │
│ │ │ - Generates: INFO, WARNING, ERROR, CRITICAL logs │ │ │
│ │ │ - Resources: cpu: 10m/25m, memory: 16Mi/32Mi │ │ │
│ │ └───────────────────────────┬─────────────────────────────────────┘ │ │
│ │ │ │ │
│ │ ▼ (stdout logs) │ │
│ └──────────────────────────────┼─────────────────────────────────────────┘ │
│ │ │
│ ▼ │
│ ┌─────────────────────────────────────────────────────────────────────────┐ │
│ │ PROMTAIL (DaemonSet) │ │
│ │ - Scrapes logs from every pod in the cluster │ │
│ │ - Adds Kubernetes labels (namespace, pod, container) │ │
│ │ - Forwards to Loki via HTTP │ │
│ └───────────────────────────┬─────────────────────────────────────────────┘ │
│ │ │
│ ▼ (HTTP Push) │
│ ┌─────────────────────────────────────────────────────────────────────────┐ │
│ │ LOKI (StatefulSet) │ │
│ │ - Stores logs in chunks │ │
│ │ - Indexes only labels (not log content) → lightweight │ │
│ │ - Accepts LogQL queries │ │
│ └───────────────────────────┬─────────────────────────────────────────────┘ │
│ │ │
│ ▼ (LogQL Query) │
│ ┌─────────────────────────────────────────────────────────────────────────┐ │
│ │ GRAFANA (Deployment) │ │
│ │ - Visualizes logs │ │
│ │ - Interactive dashboards with namespace + app filters │ │
│ │ - LogQL query editor │ │
│ └─────────────────────────────────────────────────────────────────────────┘ │
│ │
└─────────────────────────────────────────────────────────────────────────────────┘

---

## 🔄 Workflow

This diagram illustrates the end-to-end flow of log collection, storage, and visualization:

```mermaid
flowchart TD
    A[error-generator pod writes logs to stdout] --> B[Promtail DaemonSet scrapes logs]
    B --> C[Promtail adds Kubernetes labels]
    C --> D[Promtail pushes logs to Loki via HTTP]
    D --> E[Loki stores logs in chunks]
    E --> F[Loki indexes only labels]
    F --> G[Grafana queries Loki using LogQL]
    G --> H[Grafana displays dashboards]
    
    I[User applies namespace filter] --> G
    J[User applies app filter] --> G
    K[User filters by log content: ERROR, WARNING] --> G
    
    L[User queries log rate over time] --> G
    M[User queries error rate over time] --> G
    N[User parses JSON structured logs] --> G
    O[User extracts specific fields] --> G
