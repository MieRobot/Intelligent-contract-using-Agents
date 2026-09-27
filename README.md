# Enterprise Contract Intelligence on Databricks

This repository demonstrates an end-to-end **Enterprise Contract Intelligence** use case on Databricks using synthetic contract data.

The solution shows how organizations can move from fragmented contract information across systems such as **Ariba, ERP platforms, SharePoint, supplier master data, and contract repositories** toward a consolidated and governed view of:

- Supplier commitments
- Contract milestones
- Payment schedules
- SLA obligations
- Penalties
- Renewal exposure
- Contract risks
- Recommended follow-up actions

The implementation combines the **Databricks Medallion Architecture**, AI-based contract extraction, retrieval, agentic workflows, MLflow evaluation, human approval, and an auditable action queue.

---

## Solution Flow

```text
Enterprise Sources
    │
    ▼
Bronze Layer
Raw suppliers, contracts, documents, Ariba, ERP, SharePoint
    │
    ▼
AI Extraction
Structured extraction of obligations, milestones, payments, risks
    │
    ▼
Silver Layer
Normalized contract intelligence + clause-level retrieval corpus
    │
    ▼
Gold Layer
Enterprise Contract View
    │
    ├──────────────► Structured contract facts
    │
    └──────────────► Silver contract evidence
                         │
                         ▼
                 Supervisor Agent
                         │
                         ▼
                  Contract Agent
                         │
                         ▼
                    Risk Agent
                         │
                         ▼
                   Action Agent
                         │
                         ▼
              Pre-deployment MLflow Evals
                         │
                         ▼
                    Quality Gate
                         │
                         ▼
                   Human Approval
                         │
                         ▼
                    Action Queue
```

---

# Notebook Overview

## Lab A — Contract Intelligence Foundations

**Purpose:** Build the first end-to-end contract intelligence foundation using synthetic enterprise data.

### What it does

- Creates synthetic enterprise source data representing:
  - Supplier master
  - Contract metadata
  - Contract documents
  - Ariba metadata
  - ERP payment schedules
  - SharePoint / repository metadata

- Generates realistic synthetic contract text containing:
  - Supplier commitments
  - Milestones
  - Payment terms
  - SLA requirements
  - Delay penalties
  - Renewal clauses
  - Contract risks

- Attempts contract extraction using Databricks `ai_query`.

- Includes a deterministic fallback so the notebook remains runnable if model access is unavailable.

- Normalizes extracted information into:
  - obligations
  - milestones
  - payments
  - risks

- Creates clause-level contract chunks for retrieval.

- Builds a consolidated enterprise contract table.

- Attempts to create a Materialized View and falls back to a standard SQL view where required.

- Provides reusable interfaces:
  - `query_contract_data(...)`
  - `retrieve_contract_evidence(...)`

### Main idea

```text
Synthetic Sources
    → AI Extraction
    → Normalized Contract Intelligence
    → Enterprise Contract View
    → Retrieval Layer
```

---

## Lab B — Agentic Contract Intelligence Cycle

**Purpose:** Implement the complete agentic operating model on top of the contract intelligence foundation.

### Agent flow

```text
Supervisor Agent
    → Contract Agent
    → Risk Agent
    → Action Agent
    → Human Approval
    → Action Queue
```

### What it does

- Uses the Contract Agent to combine structured contract facts with retrieved contractual evidence.

- Uses the Risk Agent to assess contract exposure based on:
  - milestones
  - payments
  - SLA obligations
  - penalties
  - renewal conditions
  - identified risks

- Uses the Action Agent to propose a controlled follow-up action.

- Adds MLflow tracing for:
  - agents
  - tools
  - LLM calls
  - retriever spans

- Adds pre-deployment evaluation using:
  - `RetrievalGroundedness()`
  - `RetrievalRelevance()`
  - `Correctness()`
  - `RetrievalSufficiency()`

- Applies a quality gate before the operational workflow.

- Persists human approval decisions.

- Writes only approved actions into the Action Queue.

### Main idea

```text
Contract Facts + Evidence
    → Agent Reasoning
    → Evaluation
    → Human Governance
    → Auditable Action
```

---

## Lab C — Medallion Data and AI Foundation

**Purpose:** Rebuild the Contract Intelligence foundation using an explicit **Bronze → Silver → Gold** Databricks Medallion Architecture.

### Bronze Layer

Raw enterprise source tables:

- `bronze_suppliers`
- `bronze_contracts`
- `bronze_documents`
- `bronze_ariba`
- `bronze_erp_payments`
- `bronze_sharepoint`

These tables represent the original enterprise source landscape.

### AI Extraction

Contract documents are processed using `ai_query` where available.

The extraction converts unstructured contract text into structured information such as:

- commitments
- milestones
- payments
- SLA targets
- penalties
- renewal requirements
- risks
- recommended actions

A deterministic fallback is included for environments where the foundation model is unavailable.

### Silver Layer

Normalized contract intelligence tables:

- `silver_extractions`
- `silver_obligations`
- `silver_milestones`
- `silver_payments`
- `silver_risks`
- `silver_contract_clauses`

The Silver clause table is also the retrieval-ready corpus used by the agentic workflow.

### Gold Layer

Business-facing contract table:

- `gold_enterprise_contracts`

The Gold layer consolidates:

- supplier
- contract value
- contract dates
- next milestone
- next payment
- open obligations
- SLA target
- penalty exposure
- renewal exposure
- risk severity
- recommended action

The notebook also creates:

- `gold_enterprise_contract_view`

It attempts a Materialized View first and falls back to a standard SQL view when required.

### Main idea

```text
Sources
    → Bronze
    → AI Extraction
    → Silver
    → Gold
    → Materialized Enterprise Contract View
```

---

## Lab D — Agentic Contract Intelligence on Medallion Architecture

**Purpose:** Implement the agentic operating model directly on top of the Medallion Architecture created in Lab C.

### Data usage

**Gold**
- Used for governed structured contract facts.

**Silver**
- Used for clause-level contractual evidence and retrieval.

### Agent flow

```text
Gold + Silver Evidence
    → Supervisor Agent
    → Contract Agent
    → Risk Agent
    → Action Agent
    → MLflow Evals
    → Quality Gate
    → Human Approval
    → Action Queue
```

### What it does

- Validates that Lab C has been run.

- Queries the Gold contract layer for structured enterprise facts.

- Retrieves contract clauses from the Silver layer.

- Traces the retriever as an MLflow `RETRIEVER` span.

- Uses the Supervisor Agent to route the request.

- Uses the Contract Agent to combine:
  - Gold facts
  - Silver evidence

- Uses the Risk Agent for evidence-backed risk assessment.

- Uses the Action Agent to recommend follow-up.

- Runs the four pre-deployment evaluations:
  - `RetrievalGroundedness()`
  - `RetrievalRelevance()`
  - `Correctness()`
  - `RetrievalSufficiency()`

- Applies an explicit deployment quality gate.

- Creates a pending human approval record.

- Moves only approved actions into the Action Queue.

- Keeps an auditable governance trail.

### Main idea

```text
Medallion Data Foundation
    → Agentic Reasoning
    → Evaluation
    → Governance
    → Controlled Enterprise Action
```

---

# Recommended Execution Order

For the current architecture, use:

```text
Lab C
    ↓
Lab D
```

**Lab C** creates the Medallion data and AI foundation.

**Lab D** consumes Gold and Silver outputs and implements the governed agentic operating model.

Labs A and B represent the earlier version of the same POC before the architecture was explicitly reorganized into Bronze, Silver, and Gold layers.

They are useful for understanding how the solution evolved.

---

# Key Databricks Concepts Demonstrated

- Unity Catalog tables
- Delta Lake
- Medallion Architecture
- Bronze / Silver / Gold modeling
- Databricks `ai_query`
- Structured LLM extraction
- Contract clause chunking
- Retrieval / RAG preparation
- Materialized Views
- MLflow tracing
- Tool spans
- Retriever spans
- Agent traces
- MLflow GenAI evaluation
- Retrieval groundedness
- Retrieval relevance
- Correctness
- Retrieval sufficiency
- Human-in-the-loop controls
- Governed action queues

---

# POC Scope

The notebooks use a small synthetic enterprise contract environment suitable for experimentation in Databricks Free Edition.

Typical POC scope:

- 20–30 contracts
- 5–8 suppliers
- Synthetic Ariba metadata
- Synthetic ERP payment schedules
- Synthetic SharePoint / document repository metadata
- Synthetic contract documents
- Contract text containing realistic:
  - commitments
  - milestones
  - payments
  - SLAs
  - penalties
  - renewal terms
  - risks

The synthetic design makes it possible to validate the architecture without exposing real contract or supplier data.

---

# Example Business Questions

The solution is designed to support questions such as:

```text
Which supplier contracts require attention this week?
```

```text
Which contracts have high-risk milestones due in the next 30 days?
```

```text
What payments are due against milestones that have not yet been completed?
```

```text
Which contracts are approaching renewal windows?
```

```text
What penalty applies if a supplier misses the current milestone?
```

```text
Which high-risk contracts require procurement intervention?
```

Structured questions are answered primarily from the **Gold layer**.

Questions requiring contractual interpretation use the **Silver clause evidence layer**.

Hybrid questions combine both.

---

# Agent Responsibilities

## Supervisor Agent

Understands the request and decides:

- which contract or supplier is involved
- which structured query is required
- whether contractual evidence needs to be retrieved
- which downstream agents need to run

---

## Contract Agent

Combines:

```text
Gold structured facts
        +
Silver contractual evidence
```

The Contract Agent creates the context package used by the downstream agents.

---

## Risk Agent

Evaluates:

- delivery exposure
- payment dependencies
- milestone exposure
- SLA obligations
- penalty exposure
- renewal risk
- contract exceptions

The Risk Agent is expected to reason only from governed data and retrieved evidence.

---

## Action Agent

Creates a recommended follow-up such as:

- request supplier status update
- escalate to procurement owner
- initiate mitigation review
- validate milestone before payment
- initiate renewal workflow

The Action Agent **does not directly execute the action**.

---

# Pre-Deployment Evaluation

Before the agentic workflow is considered deployment-ready, the solution runs MLflow GenAI evaluations.

## RetrievalGroundedness

Checks whether the agent's response is supported by the retrieved contract evidence.

Useful for identifying:

- unsupported contract claims
- invented clauses
- hallucinated penalties or obligations

---

## RetrievalRelevance

Checks whether the retrieved contract clauses are relevant to the user's question.

Useful for identifying:

- noisy retrieval
- incorrect clause selection
- poor retrieval queries

---

## RetrievalSufficiency

Checks whether the retrieved evidence contains enough information to answer the expected question.

For example:

```text
Relevant evidence may have been retrieved,
but the payment clause or penalty clause may still be missing.
```

---

## Correctness

Checks the final answer against known expected contract facts.

Examples include:

- supplier
- milestone
- payment
- risk
- penalty
- recommended action

---

# Evaluation Quality Gate

The evaluation flow is:

```text
Agent code
    ↓
Evaluation dataset
    ↓
Agent traces
    ↓
Retrieval and response judges
    ↓
Quality scores
    ↓
Quality Gate
```

If the required evaluation scores do not meet the configured threshold, the workflow is not considered deployment-ready.

---

# Human-in-the-Loop Governance

The architecture deliberately separates:

```text
AI recommendation
```

from:

```text
enterprise execution
```

The operating model is:

```text
Action Agent
    ↓
Proposed action
    ↓
Human Approval
    ↓
APPROVE / REJECT
    ↓
Approved action enters Action Queue
```

The model cannot directly write an operational follow-up into the execution queue.

This provides:

- accountability
- auditability
- risk control
- human oversight
- clear separation between recommendation and execution

---

# Action Queue

Approved actions are persisted in a governed Action Queue.

A typical action record can contain:

```text
action_id
contract_id
supplier_name
action_type
owner
priority
recommended_action
rationale
supporting_evidence
queue_status
queued_at
completed_at
```

In a production implementation, this queue could integrate with systems such as:

- ServiceNow
- Jira
- procurement workflow tools
- Ariba
- ERP workflow
- Microsoft Teams
- email / notification systems

---

# End-to-End Architecture

```text
Enterprise Sources
        │
        ▼
Bronze
Raw enterprise data
        │
        ▼
AI Extraction
        │
        ▼
Silver
Normalized contract intelligence
        │
        ├──────────────► Clause retrieval
        │
        ▼
Gold
Enterprise Contract View
        │
        └──────────────┐
                       │
                       ▼
                Supervisor Agent
                       │
                       ▼
                 Contract Agent
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
        Gold Facts         Silver Evidence
             │                   │
             └─────────┬─────────┘
                       ▼
                   Risk Agent
                       │
                       ▼
                  Action Agent
                       │
                       ▼
                 MLflow Evals
                       │
                       ▼
                  Quality Gate
                       │
                       ▼
                 Human Approval
                       │
                       ▼
                   Action Queue
                       │
                       ▼
             Enterprise Follow-up
                       │
                       ▼
                Trace + Feedback
                       │
                       └────► Future evaluation cases
```

---

# Business Outcome

The solution moves enterprise contract management from:

```text
Searching individual contracts
```

toward:

```text
Enterprise Contract Intelligence
    → consolidated obligations
    → payment visibility
    → milestone visibility
    → renewal visibility
    → evidence-backed risk detection
    → agent-assisted follow-up
    → human-governed action
    → auditable execution
```

The key architectural principle is:

> **AI can extract, retrieve, reason, and recommend — while consequential enterprise actions remain governed through evaluation, quality gates, and human approval.**