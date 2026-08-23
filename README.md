# 🤖 End-to-End Agentic AI Customer Support System in Databricks

![Databricks](https://img.shields.io/badge/Databricks-Free_Edition-F3722C?logo=databricks)
![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python)
![AI](https://img.shields.io/badge/AI-Agentic_RAG-00C7B7)
![Status](https://img.shields.io/badge/Status-Complete-28A745)

This repository contains an end-to-end Agentic AI solution built entirely on the **Databricks Free Edition**. It implements a sophisticated **Retrieval-Augmented Generation (RAG)** architecture that allows an AI agent to answer customer inquiries by seamlessly querying both unstructured product documents (PDFs) and structured customer data (Delta Tables).

---

## 🏗️ Architecture Overview

The system uses an LLM (Large Language Model) as its "brain" and equips it with specialized **Tools** to retrieve information exactly when needed.

mermaid
graph TD
    subgraph "Data Ingestion"
        A[500+ PDF Documents
(Product Docs)] -->|PyPDF2 Extraction| B(Raw Text Chunks)
        C[CSV Files
(Policies, Users, Products)] -->|Ingestion| D(Delta Tables)
    end

    subgraph "Databricks Unity Catalog"
        B -->|Embedding Model| E[(Vector Search Index
Unstructured Data)]
        D -->|SQL UDFs| F[(Structured Knowledge Base)]
    end

    subgraph "Agentic AI Core"
        E -.->|Tool 1: Vector Search| G{AI Agent / LLM
(GPT-OSS)}
        F -.->|Tool 2: SQL Functions| G
    end

    subgraph "Serving & UI"
        G -->|Deployment| H[Databricks Model Serving]
        H -->|API| I[Databricks Web App
(Chat UI)]
        User((End User)) <-->|Query & Response| I
    end

    %% Styles
    classDef datastore fill:#f9f2ec,stroke:#e67e22,stroke-width:2px;
    classDef agent fill:#eafaf1,stroke:#27ae60,stroke-width:2px;
    class E,F datastore;
    class G agent;


---

## ✨ Key Features

*   **Dual-Knowledge Base Retrieval:** 
    *   **Unstructured Data:** Parses over 500 PDF product manuals, generates embeddings, and stores them in a Databricks Vector Search Index.
    *   **Structured Data:** Converts CSVs (customer details, order histories, return policies) into highly optimized Delta Tables.
*   **Tool-Calling AI Agent:** The LLM autonomously decides whether to use the `Vector Search Tool` to find tutorial links or the `SQL UDF Tool` to check a specific user's return history.
*   **Production-Ready Deployment:** The agent is deployed as an auto-scaling Databricks Model Serving endpoint (scales to zero to save resources).
*   **Built-in Web App:** Exposes the serving endpoint via a clean, interactive Databricks Chat UI for non-technical users.
*   **Observability:** Fully integrated with **MLflow Tracing** to track token usage, tool execution paths, and debug AI reasoning step-by-step.

---

## 🛠️ Technology Stack

*   **Platform:** Databricks (Free Edition)
*   **Data Governance:** Unity Catalog
*   **Data Storage:** Delta Lake, Databricks Vector Search
*   **Language Models:** GPT OSS 120B (via Databricks Foundation Model APIs)
*   **Libraries:** `PyPDF2`, `PySpark`, `mlflow`
*   **Frontend:** Databricks Apps (Chat UI)

---

## 📂 Repository Structure

text
├── Agentic_AI/
│   ├── data/
│   │   ├── pdf_docs/                # 500+ Product manuals (PDF)
│   │   └── csv_files/               # Customer service, product, and policy data
│   ├── Notebooks/
│   │   ├── 01_LLM_Interaction.py    # Basics of querying LLMs programmatically via API & SQL
│   │   ├── 02_Process_PDFs.py       # PyPDF2 extraction and saving to Delta Tables
│   │   ├── 03_Prepare_Vector_DB.py  # Data prep, creating Vector Search endpoint & indexing
│   │   ├── 04_Query_Index.py        # Hybrid searching the Vector Index via Python
│   │   └── 05_Register_Agent.py     # Registering the tool-equipped agent to Unity Catalog
│   └── README.md


---

## 🚀 Setup & Installation

### 1. Prerequisites
* A [Databricks Free Edition](https://community.cloud.databricks.com/) account.
* Create a new Catalog and Schema in Unity Catalog (e.g., `agentic_catalog.agentic_schema`).

### 2. Data Ingestion
1. Upload the `Agentic_AI/data/pdf_docs/` folder to a Databricks Volume.
2. Upload the `Agentic_AI/data/csv_files/` to create Delta tables (`customer_service`, `policies`, `products`).

### 3. Run the Data Pipelines
Execute the notebooks in order from the `Agentic_AI/Notebooks` folder:
* Run `02_Process_PDFs` to extract text from PDFs.
* Run `03_Prepare_Vector_DB` to generate embeddings and provision the Vector Search Index.
* Define SQL UDFs (User Defined Functions) on your Delta tables to allow the agent to query structured policies and service history.

### 4. Deploy the Agent
* Use the Databricks AI Playground to test tools (Vector Index + UDFs).
* Export the Playground setup as an Agent Notebook (`05_Register_Agent`).
* Run the notebook to register the model in Unity Catalog and deploy it to a **Model Serving Endpoint**.

### 5. Launch the UI
* Navigate to **Compute > Apps** in Databricks.
* Create a new "Chat UI for existing agent".
* Link it to your deployed Model Serving endpoint.
* Assign appropriate access scopes and deploy!

---

## 🧪 Example Queries

Once the app is running, try asking the agent:

> *"Where can I find tutorial videos for Account Ease Pro?"*
*(Agent uses the Vector Search Index)*

> *"Summarize the standard return policy for me."*
*(Agent uses the Policy SQL UDF)*

> *"How many returns did john

---

## 🔮 Future Enhancements

- [ ] **Multi-Agent Orchestration:** Implement LangGraph to separate the "Researcher" and "Analyst" roles.
- [ ] **Web Search Fallback:** Integrate Tavily API to allow the agent to search the internet for missing information.
- [ ] **Automated Evaluation:** Use MLflow LLM-as-a-Judge to grade the RAG system's accuracy and hallucination rate.

---
*Created as part of the Databricks End-to-End Agentic AI learning track.*
