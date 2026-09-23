# AAYUR SATHI (आयुर साथी)
### AIIA Clinical Trial Management System

> Built for the **Smart India Hackathon (SIH) Grand Finale**  
> Problem Statement by: **All India Institute of Ayurveda (AIIA), Ministry of Ayush, Government of India**

---

## Overview

**AAYUR SATHI** is a unified, cloud-native Clinical Trial Management System (CTMS) purpose-built for the All India Institute of Ayurveda (AIIA). It addresses the dual institutional mandate of:

1. **Institutional Clinical Trials** — End-to-end management of multi-center, randomized, controlled Ayurvedic clinical protocols evaluated against GCP-ASU standards.
2. **National Pharmacovigilance Coordination Centre (NPvCC)** — India's sovereign sentinel for Adverse Drug Reaction (ADR) reporting and safety surveillance across ASU&H formulations.

Previously, trial tracking, patient recruitment, milestone management, and safety surveillance existed in fragmented spreadsheets and siloed systems, creating statutory vulnerabilities including unregistered patient enrollments, delayed ADR reporting violating CDSCO's 15-day deadline, and non-interoperability with national digital health infrastructure.

---

## Key Features

- **Dual-Gate Enrollment Engine** — Backend enforces (1) CTRI registration and (2) DPDP Act 2023 Informed Consent before any patient randomization is permitted.
- **Algorithmic Pharmacovigilance** — 30-day rolling safety signal detection, synthetic MedDRA/ASU&H coding, and automated WHO-UMC causality assessment.
- **Cryptographic Merkle Blockchain Anchor** — SHA-256 Merkle tree batching over append-only audit rows, anchored on-chain to Polygon Amoy Testnet via `AuditAnchor.sol`.
- **Immutable Audit Trail** — PostgreSQL-level `BEFORE UPDATE/DELETE` triggers enforce a strictly append-only audit ledger (21 CFR Part 11 / GAMP-5 aligned).
- **Interoperability Engines** — Native CDISC SDTM v3.3 CSV export (DM, AE, IE domains + SHA-256 header), valid HL7 FHIR R4 JSON endpoints, and ABDM M1/M2 sandbox integration.
- **7 Role-Tailored Dashboards** — PI, Coordinator, Monitor, Ethics Committee, Pharmacovigilance, Admin, and Regulator (CDSCO) views across 50 functional screens.
- **Bilingual Interface** — Full English / Hindi parity with 351 synchronized localization keys and zero layout thrashing.
- **National Geo Command Map** — SVG-based tactical map with 36 state boundaries and 12 clinical trial node coordinates.

---

## Tech Stack

| Layer | Technology | Version |
|---|---|---|
| Backend Framework | Python + FastAPI | Python 3.12 / FastAPI 0.115+ |
| ORM | SQLAlchemy | 2.0.38+ |
| Database | PostgreSQL (Supabase / Local) | PostgreSQL 15+ |
| Auth & RBAC | OAuth2 Bearer + JWT | python-jose 3.3.0, passlib 1.7.4 |
| Blockchain | web3.py + Polygon Amoy Testnet | web3.py 7.8.0+ |
| Merkle Tree | pymerkle | 3.0.0+ |
| Smart Contract | Solidity + Hardhat | Solidity ^0.8.0 |
| Background Jobs | APScheduler | 3.11.0+ |
| Frontend | React + Vite | React 18.3, Vite 5.4 |
| Styling | Tailwind CSS | 3.4.1 |
| Charts | Recharts | 2.12.7 |
| Localization | Custom React Context | 351 keys (EN/HI) |

---

## System Architecture

```
Frontend (React 18 + Vite + Tailwind CSS)
    └─ 7 Role Dashboards, Geo Map, Bilingual Engine
           │ HTTPS REST + Bearer JWT
FastAPI Gateway & Security Layer
    └─ JWT Auth → Dual-Gate Enrollment → REST Controllers
           │
PostgreSQL Database (FHIR R4-aligned, 15 tables)
    └─ Immutability Trigger (append-only audit_trail)
    └─ FHIR R4 Serializer / CDISC SDTM v3.3 Engine
           │
Cryptographic Trust Layer
    └─ pymerkle → APScheduler → web3.py → Polygon Amoy Testnet
```

---

## Database Schema

15 relational tables, FHIR R4-aligned:

| Table | Purpose |
|---|---|
| `users` | RBAC user accounts (7 roles) |
| `research_study` | Clinical trial protocols |
| `study_team` | PI / Coordinator / Monitor assignments per study |
| `patient` | Patient registry with ABHA integration |
| `informed_consent` | DPDP Act 2023 digital consent records |
| `ie_criteria` / `ie_result` | Inclusion / Exclusion criteria evaluation |
| `visit_log` | Protocol visit schedule & adherence tracking |
| `protocol_deviation` | GCP deviation logging |
| `adverse_event` | NPvCC-mandated ADR/SAE reporting |
| `data_query` | GCP clinical data query engine |
| `monitoring_visit` | ALCOA+ site monitoring visit reports |
| `milestone` | Study lifecycle milestone tracker |
| `audit_trail` | Immutable 21 CFR Part 11 audit ledger |
| `audit_anchor` | Blockchain Merkle root anchor ledger |
| `notification` | Role-targeted system alerts |

---

## Getting Started

### Prerequisites

- Python 3.12+
- Node.js 18+
- PostgreSQL 15+
- A Polygon Amoy Testnet RPC URL (e.g. from Alchemy or Infura)

### Backend Setup

```bash
# Clone the repository
git clone https://github.com/your-org/sih2027-aiia-ctms-backend.git
cd sih2027-aiia-ctms-backend

# Create and activate virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Configure environment variables
cp .env.example .env
# Edit .env with your DATABASE_URL, SECRET_KEY, POLYGON_RPC_URL, etc.

# Apply database schema
psql -U postgres -d your_db_name -f schema.sql

# Start the FastAPI server
uvicorn app.main:app --reload
```

API docs available at: `http://localhost:8000/docs`

### Frontend Setup

```bash
cd frontend

# Install dependencies
npm install

# Start development server
npm run dev
```

App available at: `http://localhost:5173`

---

## RBAC — User Roles

| Role | Access Scope |
|---|---|
| `pi` (Principal Investigator) | Scoped strictly to assigned studies |
| `coordinator` | Patient enrollment, consent, visit logging |
| `monitor` | Site compliance, GCP queries, visit reports |
| `ethics_committee` | IEC review and protocol approval |
| `pharmacovigilance` | ADR/SAE management, NPvCC signal detection |
| `admin` | Full portfolio, user management, system config |
| `regulator` | Read-only CDSCO inspection, CDISC export, blockchain verification |

Demo seed credentials are available in `app/seed_data.py`.

---

## Regulatory & Compliance Features

| Standard | Implementation |
|---|---|
| **CDSCO NDCT Rules 2019** | 15-day SAE deadline enforcement with statutory countdown |
| **CTRI Registration Gate** | Hard backend gate — enrollment blocked until CTRI number confirmed |
| **DPDP Act 2023** | Digital informed consent with witness signature hash before randomization |
| **GCP-ASU** | ALCOA+ monitoring visit reports and clinical data query lifecycle |
| **21 CFR Part 11 / GAMP-5** | Append-only audit trail enforced at database trigger level |
| **HL7 FHIR R4** | `ResearchStudy`, `AdverseEvent`, `Patient` JSON endpoints |
| **CDISC SDTM v3.3** | DM, AE, IE CSV domains with `X-Export-SHA256` integrity header |
| **ABDM** | M1 (ABHA verification) and M2 (Care Context push) sandbox endpoints |

---

## Blockchain Audit Verification

Audit rows are batched periodically by APScheduler. A SHA-256 Merkle root is computed over all unanchored `row_hash` values and committed on-chain via `AuditAnchor.sol` on Polygon Amoy Testnet.

- **Verify Integrity**: `GET /audit/verify` — recomputes Merkle root and compares against on-chain value.
- **Tamper Simulation**: Available via the Regulator dashboard for live demonstration — bit-flip detected instantly with a `mismatched` status.
- **Smart Contract**: `AuditAnchor.sol` deployed on Polygon Amoy (Chain ID 80002).

---

## Automated Test Suite

Run the full verification suite:

```bash
python app/verify_full_system.py
```

**All 10 checks pass:**

| # | Test | Status |
|---|---|---|
| 1 | Authentication for all 8 user accounts | ✅ PASS |
| 2 | Multi-PI RBAC scoping & study team isolation | ✅ PASS |
| 3 | Dual-Gate patient enrollment enforcement | ✅ PASS |
| 4 | NPvCC 30-day rolling safety signal detection | ✅ PASS |
| 5 | WHO-UMC causality assessment | ✅ PASS |
| 6 | GCP clinical data query lifecycle | ✅ PASS |
| 7 | ALCOA+ site monitoring visit reports | ✅ PASS |
| 8 | CDISC SDTM CSV exports with SHA-256 header | ✅ PASS |
| 9 | ABDM M1 & M2 sandbox integration | ✅ PASS |
| 10 | Blockchain tamper simulation & verification | ✅ PASS |

Frontend build: **0 errors** in 878ms. Localization: **351/351 keys** synchronized.

---

## Hackathon Scope & Assumptions

| Domain | Hackathon Implementation | Production Requirement |
|---|---|---|
| MedDRA / WHODrug | Synthetic ASU&H dictionary (20+ terms) | Paid MSSO/WHO-UMC commercial license |
| CTRI Registry | Manual entry gate with strict backend validation | No public write API exists |
| ABDM | Validated M1 & M2 sandbox calls | Full NHA sandbox onboarding required |
| Blockchain | Polygon Amoy Testnet | Polygon Mainnet + gas funds + security audit |
| Compliance Certification | Architecture aligned with standards | Third-party organizational audit required |

---

## Project Structure

```
sih2027-aiia-ctms-backend/
├── app/
│   ├── main.py               # FastAPI application entry point
│   ├── models.py             # SQLAlchemy ORM models
│   ├── schemas.py            # Pydantic request/response schemas
│   ├── auth.py               # JWT auth & RBAC guards
│   ├── routers/              # Endpoint controllers
│   │   ├── studies.py
│   │   ├── patients.py
│   │   ├── adverse_events.py
│   │   ├── audit.py
│   │   └── fhir.py
│   ├── services/
│   │   ├── merkle_service.py # pymerkle Merkle tree logic
│   │   ├── blockchain.py     # web3.py Polygon interaction
│   │   ├── cdisc_export.py   # SDTM CSV generation
│   │   └── abdm.py           # ABDM M1/M2 sandbox
│   └── verify_full_system.py # Automated test suite
├── contracts/
│   └── AuditAnchor.sol       # Solidity smart contract
├── frontend/
│   ├── src/
│   │   ├── dashboards/       # 7 role-tailored dashboards
│   │   ├── locales/          # en.js & hi.js (351 keys each)
│   │   └── components/       # Shared UI components
│   └── vite.config.js
├── schema.sql                # Full PostgreSQL DDL
└── requirements.txt
```

---

## License

This project was developed for the Smart India Hackathon 2027. Academic and non-commercial use only.

---

<div align="center">
  Built with ❤️ for AIIA &amp; Ministry of Ayush &nbsp;|&nbsp; Smart India Hackathon Grand Finale
</div>
