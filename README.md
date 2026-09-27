# Enterprise Contract Intelligence on Databricks

This project demonstrates an end-to-end **Enterprise Contract Intelligence** use case using Databricks.

It covers contract data ingestion, AI extraction, Medallion Architecture, agentic workflows, human approval, business applications, Genie, and a semantic / ontology layer.

---

## Lab A — Contract Intelligence Foundations

Creates synthetic enterprise contract data, extracts obligations, milestones, payments, risks, and contract clauses using AI, and builds a consolidated enterprise contract view.

---

## Lab B — Agentic Contract Intelligence Cycle

Adds Supervisor, Contract, Risk, and Action Agents on top of the contract data, together with MLflow evaluation, human approval, and an Action Queue.

---

## Lab C — Medallion Data and AI Foundation

Rebuilds the contract solution using an explicit **Bronze → Silver → Gold** architecture.

- Bronze: raw supplier, contract, Ariba, ERP, SharePoint, and document data
- Silver: extracted obligations, milestones, payments, risks, and contract clauses
- Gold: consolidated enterprise contract view

---

## Lab D — Agentic Contract Intelligence on Medallion Architecture

Runs the agentic workflow directly on the Medallion model.

- Gold provides structured contract facts
- Silver provides contractual evidence
- Agents assess risk and recommend actions
- MLflow evaluates the workflow
- Approved recommendations move into the Action Queue

---

## Lab E — Contract Intelligence Business App

Creates a Streamlit-based Databricks App for business users.

The app includes:

- Executive Overview
- Contract 360
- Ask Contract Intelligence
- Human Approval Inbox
- Action Queue

---

## Lab F — Semantic and Genie Ontology Layer

Creates a Unity Catalog Metric View above the Gold contract table with governed measures, business definitions, display names, and synonyms.

This improves how **Genie Agent and Genie One** understand terms such as:

- Vendor = Supplier
- Agreement = Contract
- Commercial Exposure = Total Contract Value
- Risk Rating = Risk Severity
- Payment Exposure = Upcoming Payment Exposure

---

## Recommended Flow

**Lab C → Lab D → Lab E → Lab F**

- **Lab C:** Data and AI foundation
- **Lab D:** Agentic reasoning and governance
- **Lab E:** Business-user application
- **Lab F:** Semantic layer and Genie experience

Labs A and B represent the earlier version of the same POC and are useful for understanding the evolution of the solution.

---

## Overall Architecture

**Enterprise Sources → Bronze → Silver → Gold → Agents / App / Genie**

The solution combines:

- Structured analytics from Gold
- Contract evidence from Silver
- Agentic reasoning
- MLflow evaluation
- Human approval
- Databricks Apps
- Genie Agent
- Genie One
- Unity Catalog semantic modeling