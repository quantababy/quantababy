# 🔐 VeritasChain

> **A provenance-first fact verification and source reputation platform designed to make claims auditable, explainable, and trustworthy.**

VeritasChain is an intelligent fact-verification backend that combines **retrieval, evidence analysis, graph-based source reputation, machine learning, provenance tracking, and decentralized storage** to build a more transparent verification pipeline.

Instead of treating fact verification as simply:

```text
Claim → TRUE / FALSE
```

VeritasChain is designed around:

```text
Claim
  ↓
Evidence Retrieval
  ↓
Evidence Analysis
  ↓
Source Relationships
  ↓
Source Reputation
  ↓
Verification
  ↓
Provenance
  ↓
IPFS
  ↓
Auditable Verification Record
```

The goal is to make a verification result not only useful, but also traceable and explainable.

---

## 📌 Table of Contents
- [✨ Project Overview](#-project-overview)
- [🎯 Problem Statement](#-problem-statement)
- [💡 Solution](#-solution)
- [🏗️ Architecture](#️-architecture)
- [🔄 Verification Workflow](#-verification-workflow)
- [🧠 Core Components](#-core-components)
- [📊 Source Reputation System](#-source-reputation-system)
- [🔗 Provenance and IPFS](#-provenance-and-ipfs)
- [🧩 DSA Components](#-dsa-components)
- [🗄️ Database Layer](#️️-database-layer)
- [🌐 API Layer](#-api-layer)
- [🧪 Testing](#-testing)
- [📁 Project Structure](#-project-structure)
- [⚙️ Technology Stack](#️-technology-stack)
- [🚀 Installation](#-installation)
- [▶️ Running the Backend](#️️-running-the-backend)
- [🔧 Environment Configuration](#-environment-configuration)
- [📡 API Usage](#-api-usage)
- [📈 Current Implementation Status](#-current-implementation-status)
- [🛣️ Roadmap](#️-roadmap)
- [🔮 Future Architecture](#-future-architecture)
- [🛡️ Design Principles](#️-design-principles)
- [📌 Project Goals](#-project-goals)
- [👨‍💻 Author](#-author)
- [📄 License](#-license)

---

## ✨ Project Overview
VeritasChain is being developed as a fact-verification and trust infrastructure platform.
The system focuses on three major questions:

**1️⃣ Is the claim supported?**
The system retrieves and processes evidence related to a submitted claim.

**2️⃣ How trustworthy are the sources?**
Rather than treating every source equally, VeritasChain builds a source relationship graph and calculates reputation using both:
*   📈 PageRank
*   🤖 Graph Neural Networks

**3️⃣ Can the verification result be audited?**
Verification results are converted into structured provenance records and can be stored through IPFS using Pinata.
This creates a chain of evidence:

```text
Claim
 ↓
Evidence
 ↓
Sources
 ↓
Reputation
 ↓
Verification
 ↓
Provenance
 ↓
IPFS Content Identifier
```

---

## 🎯 Problem Statement
Modern information verification systems face several problems:
*   ❌ Sources are often treated equally.
*   ❌ Verification results can be difficult to audit.
*   ❌ Evidence may be separated from the final verdict.
*   ❌ Source-to-source relationships are often ignored.
*   ❌ A simple TRUE/FALSE result provides limited explanation.
*   ❌ Verification history can be difficult to reproduce.
*   ❌ Trust is often represented as an opaque number.

VeritasChain attempts to address these problems by combining:

**Information Retrieval + Evidence Processing + Graph Algorithms + Graph Neural Networks + Provenance + Decentralized Storage**

---

## 💡 Solution
VeritasChain introduces a layered verification architecture.

```text
                    ┌────────────────────┐
                    │       CLAIM        │
                    └─────────┬──────────┘
                              │
                              ▼
                    ┌────────────────────┐
                    │ Evidence Retrieval │
                    └─────────┬──────────┘
                              │
                              ▼
                    ┌────────────────────┐
                    │ Evidence Processing│
                    └─────────┬──────────┘
                              │
                              ▼
                    ┌────────────────────┐
                    │ Source Graph       │
                    └─────────┬──────────┘
                              │
                 ┌────────────┴────────────┐
                 ▼                         ▼
          ┌──────────────┐         ┌──────────────┐
          │   PageRank   │         │     GNN      │
          │ Reputation   │         │ Reputation   │
          └──────┬───────┘         └──────┬───────┘
                 │                        │
                 └───────────┬────────────┘
                             ▼
                  ┌────────────────────┐
                  │ Unified Reputation │
                  └─────────┬──────────┘
                            │
                            ▼
                  ┌────────────────────┐
                  │ Verification       │
                  └─────────┬──────────┘
                            │
                            ▼
                  ┌────────────────────┐
                  │ Provenance Record  │
                  └─────────┬──────────┘
                            │
                            ▼
                  ┌────────────────────┐
                  │        IPFS        │
                  └────────────────────┘
```

---

## 🏗️ Architecture
The current backend architecture is divided into several logical layers.

```text
app/
│
├── api/
│   └── routes/
│
├── dsa/
│
├── gnn/
│
├── models/
│
├── repositories/
│
├── schemas/
│
└── services/
```
Each layer has a specific responsibility.

---

## 🔄 Verification Workflow
The conceptual verification flow is:

```text
                USER CLAIM
                    │
                    ▼
             ┌─────────────┐
             │ ClaimService│
             └──────┬──────┘
                    │
                    ▼
       ┌────────────────────────┐
       │ Verification Coordinator│
       └────────────┬───────────┘
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
   Retrieval     Evidence    Ranking
        │           │           │
        └───────────┼───────────┘
                    │
                    ▼
              ┌───────────┐
              │ Reasoning │
              └─────┬─────┘
                    │
                    ▼
             ┌─────────────┐
             │ Verification│
             └──────┬──────┘
                    │
                    ▼
                 VERDICT
                    │
                    ▼
               PROVENANCE
                    │
                    ▼
                  IPFS
```
The architecture is intentionally service-oriented so that retrieval, ranking, reasoning, provenance, and reputation remain independently testable.

---

## 🧠 Core Components

### 1. Claim Processing
The claim layer manages incoming verification requests.
The claim service coordinates the necessary downstream operations.
```text
Input Claim → Validate → Process → Verification Pipeline
```

### 2. Evidence Retrieval
The retrieval layer is responsible for obtaining evidence relevant to the submitted claim.
```text
Claim → Search / Retrieval → Candidate Sources → Evidence
```
Retrieved information becomes part of the downstream verification process.

### 3. Evidence Processing
Evidence is processed independently from the claim itself. This allows the system to maintain separation between Claim, Evidence, Source, Verification, and Provenance.

### 4. Ranking
Evidence and sources can be ranked before reasoning. The ranking layer allows the system to prioritize relevant evidence instead of treating all retrieved information equally.

### 5. Reasoning
The reasoning layer consumes the processed evidence and contributes to the verification decision. The architecture intentionally keeps reasoning separate from retrieval and storage.

### 6. Verification
The verification service produces the verification result based on the available evidence and reasoning pipeline. The final result can then be connected to provenance information.

---

## 📊 Source Reputation System
One of the central features of VeritasChain is the source reputation architecture. Instead of treating sources as simply "Trusted" or "Untrusted", the system represents relationships between sources as a graph.

### 🕸️ SourceGraph
The source graph is a directed weighted graph.

```text
Source A
   │
   │ citation (weight: 0.8)
   ▼
Source B
   │
   │ citation (weight: 0.5)
   ▼
Source C
```
The graph is implemented using NetworkX.

### 📈 PageRank Reputation
The first reputation signal is graph-based PageRank.
```text
Source relationships → Directed weighted graph → PageRank → Source reputation
```
PageRank provides a graph-structural reputation signal. It answers: *How important is this source relative to the structure of the source network?*

### 🤖 GNN Reputation
The second reputation signal is generated using a Graph Neural Network.
```text
Source Graph → Graph Features → GCN → Source Embeddings → Reputation Head → Reputation Score
```
The resulting score is normalized into `0.0 (Low) ─────────────── 1.0 (High)`.

### 🧬 GNN Architecture
The current GNN uses a two-layer Graph Convolutional Network.

```text
Input Features
      │
      ▼
┌──────────────┐
│   GCNConv    │
└──────┬───────┘
       │
       ▼
     ReLU
       │
       ▼
    Dropout
       │
       ▼
┌──────────────┐
│   GCNConv    │
└──────┬───────┘
       │
       ▼
Source Embedding
       │
       ▼
Reputation Head
       │
       ▼
    Sigmoid
       │
       ▼
  [0, 1] Score
```
The GCN also incorporates citation edge weights during message passing.

### 📚 GNN Training Pipeline
The project contains a dedicated training pipeline.

```text
Historical Reputation Statistics
              │
              ▼
    Training Dataset Builder
              │
              ▼
       Graph Data Object
              │
              ▼
       Dataset Splitter
          /        \
         /          \
     Training     Validation
        │              │
        ▼              │
      Trainer          │
        │              │
        ▼              │
     GCN + Head        │
        │              │
        └──────┬───────┘
               ▼
           Evaluator
               │
               ▼
          MSE / MAE
```

### 💾 GNN Model Persistence
Trained GNN components can be saved as a checkpoint.
```text
Checkpoint
├── Format Version
├── Model Configuration
├── Reputation Head Configuration
├── GCN State Dictionary
└── Reputation Head State Dictionary
```

---

## 🔗 Provenance and IPFS
VeritasChain is designed around the principle that a verification result should have a traceable provenance record.

```text
Claim → Evidence → Sources → Verification → Provenance Record → Canonical Representation → IPFS → CID
```

### 📦 IPFS Integration
The project contains an IPFS service and Pinata provider.

```text
Application
     │
     ▼
IPFSService
     │
     ▼
PinataProvider
     │
     ▼
IPFS Network
     │
     ▼
Content Identifier
```
The system uses canonicalized provenance data so that equivalent provenance records can produce deterministic content representations.

---

## 🧩 DSA Components
Data structures and graph algorithms are not treated as isolated academic components; they are part of the application architecture.

### 🌳 ClaimTrie
Provides a trie-based structure for claim-related operations.
```text
             root
            /    \
           c      t
          /        \
         l          h
         |
         a
         |
         i
         |
         m
```

### 📋 ClaimQueue
Provides queue-based claim processing for workers.
```text
Incoming Claims
      │
      ▼
┌──────────────┐
│ Claim Queue  │
├──────────────┤
│ Claim 1      │
│ Claim 2      │
│ Claim 3      │
└──────┬───────┘
       │
       ▼
Verification Worker
```

### 🕸️ SourceGraph
Represents relationships between sources, providing PageRank calculations and graph serialization.

---

## 🗄️ Database Layer
The backend currently uses: **SQLAlchemy + SQLite**.

The database layer provides:
```text
Database
   │
   ├── Models
   ├── Repositories
   └── Services
```
Repositories separate persistence operations from business logic (Claims, Evidence, Sources, Verification, Provenance).

---

## 🌐 API Layer
The backend is built using FastAPI.

Current API organization:
```text
app/api/routes/
│
├── health.py
├── claims.py
└── reputation.py
```

### ⭐ Source Reputation API
The current reputation API exposes source reputation through:
`GET /sources/reputation?url=https://example.com`

The response combines PageRank Reputation and GNN Reputation into a Unified Reputation.

---

## 🧪 Testing
Testing is a major part of the current implementation. The project uses automated tests to protect the backend architecture as new components are introduced.

Testing covers:
API, Claim Services, Verification, Repositories, Source Graph, Citation Graph, Provenance, IPFS, Pinata, GNN Pipeline, Model persistence, and Unified Reputation.

*   **Latest combined GNN regression run passed:** 116 tests passed.
*   **Broader backend regression suite reached:** 284 tests passed before subsequent additions.

---

## 📁 Project Structure
Current backend structure:

```text
backend/
│
├── app/
│   │
│   ├── api/
│   │   └── routes/
│   │       ├── health.py
│   │       ├── claims.py
│   │       └── reputation.py
│   │
│   ├── core/
│   │
│   ├── db/
│   │
│   ├── dsa/
│   │
│   ├── gnn/
│   │
│   ├── models/
│   │
│   ├── repositories/
│   │
│   ├── schemas/
│   │
│   └── services/
│
├── tests/
├── .env
└── veritaschain.db
```

---

## ⚙️ Technology Stack

**Backend:** 🐍 Python | ⚡ FastAPI | 🗄️ SQLAlchemy | 🧪 Pytest
**Data Structures / Algorithms:** 🌳 Trie | 📋 Queue | 🕸️ NetworkX | 📈 PageRank | Graph-based algorithms
**Machine Learning:** 🔥 PyTorch | 🔥 PyTorch Geometric | 🧠 Graph Convolutional Networks | 📊 MSE / MAE
**Storage:** 🗄️ SQLite | 📦 IPFS | ☁️ Pinata
**Architecture:** Dependency Injection | Service Layer | Repository Pattern | Schema/Model separation | Modular ML pipeline

---

## 🚀 Installation

1. Clone the repository
```bash
git clone <repository-url>
cd veritaschain
```

2. Create virtual environment
```bash
python -m venv .venv
```
*(Windows)*: `.venv\Scripts\Activate.ps1`

3. Install dependencies
```bash
pip install -r requirements.txt
```

---

## ▶️ Running the Backend
From the backend directory:
```bash
uvicorn app.main:app --reload
```
*   **API available at:** `http://127.0.0.1:8000`
*   **FastAPI documentation:** `http://127.0.0.1:8000/docs`

---

## 🔧 Environment Configuration
VeritasChain uses environment variables for configuration. Example:
```env
DATABASE_URL=sqlite:///./veritaschain.db
```
⚠️ **Security:** Never commit API keys, JWT tokens, private keys, passwords, or provider secrets to Git. Use `.env` and keep it excluded through `.gitignore`.

---

## 📡 API Usage

**Source Reputation Example Request:**
```http
GET /sources/reputation?url=https://example.com
```

**Conceptual Response:**
```json
{
  "url": "https://example.com",
  "pagerank_reputation": 0.82,
  "gnn_reputation": 0.91
}
```

---

## 📈 Current Implementation Status

### ✅ Implemented
*   FastAPI backend & REST API
*   SQLite + SQLAlchemy
*   Claim processing & Verification pipeline
*   Evidence processing, Retrieval, Ranking, Reasoning
*   ClaimTrie, ClaimQueue, SourceGraph, Citation graph
*   PageRank reputation & Unified reputation
*   GNN model, head, dataset generation, training, evaluation, persistence
*   Provenance, IPFS service, Pinata provider
*   Automated regression testing

### 🟡 Partially Implemented
**GNN Production Runtime**
The GNN model persistence mechanism exists and can load trained checkpoints. The remaining task is connecting the trained checkpoint to the FastAPI runtime.
```text
Checkpoint → Persistence Service (✅) → Loaded Model (✅) → GNN Reputation Service (✅) → FastAPI Runtime (🔄 Integration pending)
```

### ⏳ Not Yet Implemented
*   React frontend & WebSocket layer
*   Solidity smart contracts, Hardhat, Web3.py, Polygon testnet
*   On-chain provenance verification
*   Frontend graph visualization & verdict dashboard
*   Redis, Production PostgreSQL, Docker & Cloud deployment

---

## 🛣️ Roadmap

| Phase | Description | Status |
| :--- | :--- | :--- |
| **Phase 1** | Backend Foundation (FastAPI → DB → Services → Verification Pipeline) | ✅ Completed |
| **Phase 2** | Source Intelligence (SourceGraph → Citation Graph → PageRank) | ✅ Completed |
| **Phase 3** | GNN Reputation (Graph Data → GCN → Reputation Head) | 🟢 Training implemented <br> 🔄 Runtime pending |
| **Phase 4** | Decentralized Provenance (Verification → Provenance → IPFS → CID) | 🟢 IPFS implemented <br> ⏳ Blockchain pending |
| **Phase 5** | Blockchain (Solidity + Hardhat + Web3.py + Polygon) | ⏳ Planned |
| **Phase 6** | Frontend (React, Dashboards, Verdict visualization) | ⏳ Planned |
| **Phase 7** | Production Infrastructure (PostgreSQL, Redis, Docker, Cloud) | ⏳ Planned |

---

## 🔮 Future Architecture
The long-term VeritasChain architecture is:

```text
                         ┌───────────────┐
                         │    CLIENT     │
                         └───────┬───────┘
                                 │
                                 ▼
                         ┌───────────────┐
                         │    REACT      │
                         │   FRONTEND    │
                         └───────┬───────┘
                                 │
                                 ▼
                         ┌───────────────┐
                         │    FASTAPI    │
                         │   API LAYER   │
                         └───────┬───────┘
                                 │
                 ┌───────────────┼───────────────┐
                 │               │               │
                 ▼               ▼               ▼
           Verification      Reputation      Provenance
                 │               │               │
                 │         ┌─────┴─────┐         │
                 │         │           │         │
                 │      PageRank      GNN        │
                 │         │           │         │
                 │         └─────┬─────┘         │
                 │               │               │
                 └───────────────┼───────────────┘
                                 │
                                 ▼
                           ┌───────────┐
                           │   IPFS    │
                           └─────┬─────┘
                                 │
                                 ▼
                         ┌───────────────┐
                         │  BLOCKCHAIN   │
                         └───────────────┘
```

---

## 🛡️ Design Principles

1.  **🔍 Explainability:** A verification result should be connected to evidence and sources.
2.  **🧩 Modularity:** Each major subsystem has a dedicated responsibility (API → Services → Repositories → Database).
3.  **🧪 Testability:** Services are designed with dependency injection.
4.  **🔐 Provenance First:** Verification is designed to produce: `Claim → Evidence → Verification → Provenance`.
5.  **📦 Reproducibility:** Important artifacts use versioned checkpoints containing architecture metadata and model state.
6.  **🚀 Separation of Concerns:** Avoids placing business logic directly inside API routes.

---

## 📌 Project Goals
The long-term objective is to create a verification platform where users can answer:
*   ❓ What is being claimed? *(Claim)*
*   ❓ What evidence supports the claim? *(Evidence)*
*   ❓ Which sources are involved? *(Source Graph)*
*   ❓ How trustworthy are those sources? *(PageRank + GNN)*
*   ❓ What process produced the verdict? *(Verification Pipeline)*
*   ❓ Can the result be independently referenced? *(Provenance → IPFS → Blockchain)*

---

## 📊 Project Maturity

| Layer | Status |
| :--- | :--- |
| Backend foundation | 🟢 Implemented |
| Database | 🟢 Implemented |
| Verification pipeline | 🟢 Implemented |
| DSA layer | 🟢 Implemented |
| Source graph | 🟢 Implemented |
| PageRank reputation | 🟢 Implemented |
| GNN architecture | 🟢 Implemented |
| GNN training | 🟢 Implemented |
| GNN evaluation | 🟢 Implemented |
| GNN persistence | 🟢 Implemented |
| GNN runtime integration | 🟡 In progress |
| Provenance | 🟢 Implemented |
| IPFS / Pinata | 🟢 Implemented |
| Blockchain | 🔵 Planned |
| React frontend | 🔵 Planned |
| WebSocket | 🔵 Planned |
| Production infrastructure | 🔵 Planned |

---

## 👨‍💻 Author
**Prabhat**
*Computer Science Engineering — Artificial Intelligence*

VeritasChain is being developed as a deep engineering project combining:
*Software Engineering + Data Structures & Algorithms + Machine Learning + Graph Neural Networks + Distributed Systems + Decentralized Storage + Blockchain*

---

## 📄 License
License information will be added as the project approaches public release.

---

### Important Notes for Portfolio Use

For your **portfolio**, it is recommended to use this README as the **technical repository README**, but the portfolio case study itself should be shorter and more visual.

The strongest portfolio description would emphasize the implemented engineering:

> **VeritasChain** is a fact-verification backend combining evidence processing, weighted source graphs, PageRank, Graph Neural Networks, and provenance tracking. The system generates source reputation through both graph-based and learned signals, while verified provenance can be persisted through IPFS/Pinata. The backend is built with FastAPI, SQLAlchemy, NetworkX, PyTorch Geometric, and a modular service/repository architecture with extensive automated testing.

*(Note: Ensure you do not currently claim React, blockchain, WebSockets, or production deployment as completed features in your portfolio until they are moved from roadmap to implemented.)*
