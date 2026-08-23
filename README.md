# 🤖 Agentic AI Customer Support Assistant

### End-to-End Hybrid RAG System on Databricks

> An AI customer-support assistant that combines product-manual knowledge with customer-specific data and autonomously selects the right tool to answer each question.

[![Databricks](https://img.shields.io/badge/Databricks-Lakehouse-red?logo=databricks)](https://www.databricks.com/)
[![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python)](https://www.python.org/)
[![Apache Spark](https://img.shields.io/badge/Apache%20Spark-PySpark-orange?logo=apachespark)](https://spark.apache.org/)
[![MLflow](https://img.shields.io/badge/MLflow-Observability-blue?logo=mlflow)](https://mlflow.org/)

## 📌 Project Overview

Customer-support questions often require more than one type of information.

For example:

> **"Can I return my laptop, and have I already raised a service request for it?"**

A useful answer requires both:

- **Unstructured knowledge** — return policies and product manuals.
- **Structured data** — customer and service history.

This project solves that problem using an **Agentic RAG architecture** on Databricks. The LLM acts as the orchestrator, choosing between **Vector Search** and **Unity Catalog SQL functions**, then combining the results into a grounded response.

---

## 🎯 What the Project Does

- Processes **500+ product manuals** into a searchable knowledge base.
- Uses **Databricks Vector Search** for semantic retrieval.
- Stores structured customer/service data in **Delta Tables**.
- Exposes structured data through **Unity Catalog SQL functions**.
- Lets the agent decide which tool(s) to use based on the user's intent.
- Uses **MLflow tracing** to inspect agent and tool execution.
- Deploys the solution through **Model Serving + Databricks Apps**.

---

## 🧠 Why Agentic RAG?

A traditional RAG application usually follows a fixed flow:

```text
Question → Retrieve Documents → LLM → Answer
```

This project uses a dynamic workflow:

```text
                 ┌──────────────────┐
                 │   User Question  │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │   Agent / LLM    │
                 │ Understand Intent│
                 │ Select Tool(s)   │
                 └───────┬────┬─────┘
                         │    │
                ┌────────┘    └─────────┐
                ▼                       ▼
       ┌─────────────────┐     ┌─────────────────┐
       │  Vector Search  │     │  SQL UC Tools   │
       │ Product Manuals │     │ Customer Data   │
       └────────┬────────┘     └────────┬────────┘
                │                       │
                └──────────┬────────────┘
                           ▼
                  ┌──────────────────┐
                  │ Grounded Answer  │
                  └──────────────────┘
```

**The important distinction:** the LLM is not limited to one retrieval path. It can choose the appropriate trusted source and combine multiple sources when needed.

---

## 🏗️ Architecture


---

## ✨ Core Capabilities

### 1. Hybrid RAG

| Data | Storage / Source | Tool | Example |
|---|---|---|---|
| Product knowledge | PDFs → Delta | Vector Search | "How do I reset the device?" |
| Customer data | Delta Tables | SQL UC Function | "Show my service history." |
| Mixed question | Both | Multiple tools | "Can I return it and do I have an open service ticket?" |

### 2. Agentic Tool Calling

The agent receives trusted tools and decides which one is relevant instead of following a hard-coded retrieval path.

### 3. Grounded Responses

The final answer is generated from retrieved enterprise data rather than relying only on the model's internal knowledge.

### 4. Observability

MLflow tracing helps inspect the execution path, including model calls, tool calls, retrieval steps, latency, and token usage.

---

## 📚 Data Pipeline

### Unstructured Data

```text
PDF Manuals
    ↓
Databricks Volumes
    ↓
PyPDF2 / Python
    ↓
Text Cleaning + Metadata Enrichment
    ↓
Delta Table
    ↓
Embeddings
    ↓
Databricks Vector Search
```

### Structured Data

```text
CSV / Structured Data
        ↓
   Delta Tables
        ↓
   Unity Catalog
        ↓
 SQL Functions / UDFs
        ↓
    Agent Tools
```

Example tools:

```sql
get_return_policy()
get_service_history(user_email)
```

---

## ⚙️ End-to-End Workflow

### Step 1 — Ingest

Upload PDFs and structured datasets into Databricks Volumes.

### Step 2 — Process Documents

Extract PDF text with Python/PyPDF2, clean it, and enrich it with product metadata.

### Step 3 — Build the Knowledge Base

Store the processed content in Delta and create a Vector Search index using embeddings.

### Step 4 — Create Structured Tools

Expose customer and service information through governed Unity Catalog SQL functions.

### Step 5 — Configure the Agent

Give the LLM access to the Vector Search index and SQL functions. The agent is instructed to select the appropriate tool(s), use retrieved evidence, and synthesize the response.

### Step 6 — Deploy

Register the agent/model in Unity Catalog and deploy it through a Databricks Model Serving endpoint.

### Step 7 — Connect the App

Expose the endpoint through a Databricks Chat App.

### Step 8 — Trace

Use MLflow tracing to inspect agent execution and debug retrieval or tool-selection issues.

---

## 🔎 Example Queries

### Document Question

**User:**
> How do I troubleshoot a battery charging issue?

**Agent:**
1. Identifies this as a product-knowledge question.
2. Searches Vector Search.
3. Retrieves relevant manual content.
4. Generates a grounded answer.

### Structured Question

**User:**
> What service requests have I raised recently?

**Agent:**
1. Identifies this as a customer-specific request.
2. Calls `get_service_history(user_email)`.
3. Summarizes the returned records.

### Multi-Tool Question

**User:**
> Can I return my product, and have I already opened a service ticket for it?

**Agent:**
1. Retrieves the return policy.
2. Checks service history.
3. Combines both results into one answer.

---

## 🛠️ Tech Stack

| Area | Technologies |
|---|---|
| Cloud / Data Platform | Databricks, Apache Spark, Delta Lake, Unity Catalog, Databricks Volumes |
| GenAI | LLMs, RAG, Embeddings, Databricks Vector Search, Agentic Tool Calling |
| Backend | Python, SQL, PyPDF2 |
| MLOps | MLflow Tracing, Model Serving |
| Frontend | Databricks Apps |

---

## 🧩 Key Design Decisions

### Why Vector Search?

Product manuals are unstructured, so semantic retrieval is a better fit than simple keyword matching.

### Why SQL Functions?

Customer and service information is structured and transactional. SQL functions provide deterministic access to the governed source instead of asking the LLM to guess or generate that data.

### Why Unity Catalog?

Unity Catalog provides a governed layer for enterprise data and functions.

### Why Agentic Orchestration?

Support questions can span different data sources. The agent can dynamically choose the relevant tool instead of forcing every question through the same retrieval pipeline.

### Why MLflow Tracing?

Agentic workflows involve multiple model and tool steps. Tracing makes it easier to identify whether an issue came from retrieval, tool selection, prompting, or generation.

---

## 🏁 Conclusion

This project demonstrates how to move beyond a basic chatbot and build an **Agentic AI system that can reason over multiple enterprise data sources**.

```text
Enterprise Data
      ↓
Data Engineering
      ↓
Knowledge + Tools
      ↓
Agentic LLM
      ↓
Hybrid Retrieval
      ↓
Grounded Response
      ↓
Observability + Deployment
```

> **Key takeaway:** the system does not simply generate an answer. It decides how to find the answer, uses governed enterprise tools, and combines the results into a useful response.

---
