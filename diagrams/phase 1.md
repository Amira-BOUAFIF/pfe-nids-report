# MVP Definition — NIDS Web Application
**Project:** Network Intrusion Detection System Powered by Machine Learning  
**Date:** 2026-02-24  
**Phase:** 1 — Analyse et Conception

---

## 1. MVP Objective

Deliver a realistic, stable, and functional NIDS prototype that:
- Analyzes network flows using a trained ML model
- Detects and classifies network attacks in near real-time
- Displays alerts and statistics via a web dashboard
- Allows the Network/Security Administrator to manage alerts
- Allows the IT Manager to consult reports

---

## 2. Dataset

| Property | Value |
|---|---|
| Dataset | CIC-IDS2017 |
| Source | Canadian Institute for Cybersecurity |
| Format | CSV (pre-extracted features) |
| Size | ~2.8M records across 8 CSV files |
| Attack types covered | DoS, Probe, Brute Force, Web Attack, Botnet, Infiltration |

**Preprocessing steps included in MVP:**
- Remove null and infinite values
- Normalize numerical features (Min-Max or StandardScaler)
- Encode labels (binary: BENIGN / ATTACK + multiclass: attack category)
- Handle class imbalance (SMOTE or class weighting)
- Train/test split: 80/20

---

## 3. Models

| Model | Role | Status |
|---|---|---|
| Decision Tree | Baseline — interpretable reference | ✅ MVP |
| XGBoost | Main model — best performance/speed tradeoff | ✅ MVP |
| Autoencoder | Anomaly detection for unknown attacks | ⚠️ If planning allows |

**Model retained for production integration:** XGBoost

**Justification:** XGBoost offers the best balance between:
- High accuracy on CIC-IDS2017 benchmark
- Fast inference time suitable for near real-time detection
- Low memory footprint for API deployment via FastAPI

---

## 4. Target Metrics

| Metric | Target Value | Justification |
|---|---|---|
| Accuracy | ≥ 98% | Standard benchmark on CIC-IDS2017 |
| F1-Score (macro) | ≥ 0.95 | Handles class imbalance across attack types |
| False Positive Rate | ≤ 2% | Critical — too many false alerts = alert fatigue |
| False Negative Rate | ≤ 1% | Critical — missed attacks = security breach |
| Inference time | ≤ 200ms per flow | Near real-time requirement from CdC 5.3 |

---

## 5. Exact Features Included in MVP

### 5.1 AI / Detection Engine
- [ ] Data preprocessing pipeline (cleaning, normalization, encoding)
- [ ] Decision Tree training and evaluation
- [ ] XGBoost training and evaluation
- [ ] Model serialization (.pkl file)
- [ ] FastAPI microservice exposing POST /predict endpoint
- [ ] Input: CIC-IDS2017 feature vector (78 features)
- [ ] Output: { label: String, confidence: float }

### 5.2 Backend (Spring Boot)
- [ ] REST API for alert management (CRUD)
- [ ] REST API for network flow submission
- [ ] Integration with FastAPI via HTTP client (RestTemplate / WebClient)
- [ ] Alert persistence in MySQL
- [ ] NetworkFlow log persistence in MySQL
- [ ] AuditLog persistence in MySQL
- [ ] Report auto-generation (weekly)
- [ ] SSE endpoint for real-time alert push to frontend
- [ ] JWT authentication (login / logout)
- [ ] Role-based access control (ADMIN / IT_MANAGER)

### 5.3 Frontend (React)
- [ ] Login page
- [ ] Dashboard — real-time alert counter, attack type distribution chart, flow volume over time
- [ ] Statistics Summary page — aggregated metrics
- [ ] Alerts List page — paginated, searchable
- [ ] Alert Detail page — full flow data + status management (REVIEWED / DISMISSED)
- [ ] Reports page — view auto-generated reports + export
- [ ] Real-time updates via SSE

### 5.4 Database (MySQL)
Tables:
- [ ] `user`
- [ ] `alert`
- [ ] `attack_type`
- [ ] `network_flow`
- [ ] `audit_log`
- [ ] `report`
- [ ] `configuration`

---

## 6. Features Explicitly Excluded from MVP (Future Work)

| Feature | Reason |
|---|---|
| Autoencoder model | Time constraint — added only if planning allows |
| Transformer model | Post-MVP complexity |
| Complex model ensemble | Post-MVP |
| Kubernetes deployment | Post-MVP scalability |
| Docker containerization | Post-MVP |
| InfluxDB + Grafana | Post-MVP observability |
| Multi-dataset benchmark | Post-MVP |
| Account self-management (change password, email) | Not in CdC scope |
| Live packet capture (pcap) | Post-MVP — CIC-IDS2017 CSV used instead |

---

## 7. Technology Stack

| Layer | Technology | Version |
|---|---|---|
| ML / AI | Python, Scikit-learn, XGBoost | Python 3.10+ |
| AI Microservice | FastAPI | 0.100+ |
| Backend | Spring Boot | 3.x |
| Database | MySQL | 8.x |
| Frontend | React | 18.x |
| Authentication | Spring Security + JWT | — |
| Real-time | Server-Sent Events (SSE) | — |
| Model serialization | Joblib (.pkl) | — |

---

## 8. Division of Work

| Responsibility | Who |
|---|---|
| Data preprocessing + model training + evaluation | Both |
| FastAPI microservice | Both |
| Spring Boot backend + APIs + DB | Teammate |
| React frontend + dashboard + alerts UI | Amira |

---

## 9. MVP Delivery Criteria

The MVP is considered complete when:
1. XGBoost model achieves target metrics on CIC-IDS2017 test set
2. FastAPI /predict endpoint responds in ≤ 200ms
3. Spring Boot correctly stores flows, generates alerts, persists logs
4. React dashboard displays real-time alerts via SSE
5. Admin can manage alert status (REVIEWED / DISMISSED)
6. IT Manager can view and export auto-generated reports
7. Authentication works with role-based access control