# 🏢 Enterprise RAG Intelligence System

> **Author:** Abhay Gupta  
> **GitHub:** [github.com/Abs6187](https://github.com/Abs6187)  
> **LinkedIn:** [linkedin.com/in/abhay-gupta-197b17264](https://www.linkedin.com/in/abhay-gupta-197b17264/)  
> **Twitter/X:** [x.com/AbhayGu19265651](https://x.com/AbhayGu19265651)  
> **© 2026 Abhay Gupta. All Rights Reserved.**

---

A production-grade Retrieval-Augmented Generation (RAG) pipeline with **Role-Based Access Control (RBAC)**, multi-format data ingestion, hybrid retrieval, and verifiable citations.

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    USER QUERY INTERFACE                      │
│              (Natural Language Question + UserID)            │
└──────────────────────────┬──────────────────────────────────┘
                           │
                ┌──────────▼──────────┐
                │   IDENTITY & RBAC   │
                │   Role Resolution   │
                └──────────┬──────────┘
                           │
              ┌────────────▼────────────┐
              │     INTENT ROUTING      │
              │  (Classify data source) │
              └────────────┬────────────┘
                           │
         ┌─────────────────┼─────────────────┐
         ▼                 ▼                 ▼
   ┌──────────┐     ┌──────────┐     ┌──────────┐
   │   PDF    │     │  CSV/SQL │     │   JSON   │
   │  Vector  │     │  Vector  │     │  Vector  │
   │  Store   │     │  Store   │     │  Store   │
   └─────┬────┘     └─────┬────┘     └─────┬────┘
         │                 │                 │
         └─────────────────┼─────────────────┘
                           │
              ┌────────────▼────────────┐
              │  RBAC PRE-FILTERING     │
              │  (metadata filter on    │
              │   allowed_roles field)  │
              └────────────┬────────────┘
                           │
              ┌────────────▼────────────┐
              │  LLM GENERATION WITH    │
              │  CITATIONS & CONFIDENCE │
              │  (DeepSeek V4 Flash)    │
              └─────────────────────────┘
```

## Project Structure

```
HackerEarth_Simplify/
├── data/
│   ├── HR_Policy.pdf              # HR policy document (auto-generated)
│   ├── HR_Policy_Content.txt      # Source text for PDF generation
│   ├── Financial_Records.csv      # Quarterly revenue & expenses
│   ├── System_Logs.json           # System error logs with timestamps
│   └── User_Roles.json            # RBAC user→role mappings
├── Enterprise_RAG_System.ipynb    # Main Jupyter notebook (full pipeline)
├── enterprise_rag_system.py       # Python script version
├── requirements.txt               # Python dependencies
├── .env.example                   # Environment variable template
└── README.md                      # This file
```

## Quick Start

### 1. Install Dependencies

```bash
pip install -r requirements.txt
```

### 2. Configure API Key

Set your DeepSeek API key as an environment variable:

```bash
# Windows
set OPENAI_API_KEY=your-deepseek-api-key-here

# Linux/Mac
export OPENAI_API_KEY=your-deepseek-api-key-here
```

> **Note:** The system works in **offline mode** without an API key. It uses a rule-based fallback generator for demonstrations. To enable full LLM-powered generation, get a DeepSeek API key from [platform.deepseek.com](https://platform.deepseek.com).

### 3. Run the Notebook

```bash
jupyter notebook Enterprise_RAG_System.ipynb
```

Run all cells sequentially. The notebook will:
1. Generate `HR_Policy.pdf` from the source text
2. Ingest all data files and tag them with RBAC metadata
3. Build a ChromaDB vector store with built-in embeddings
4. Demonstrate RBAC-filtered queries for different users
5. Generate cited answers with confidence scores

## Key Features

| Feature | Implementation |
|---|---|
| **Multi-Format Ingestion** | PDF (PyMuPDF), CSV (pandas), JSON (native) |
| **RBAC Pre-Filtering** | Metadata-level filtering at retrieval stage |
| **Vector Search** | ChromaDB with built-in default embeddings |
| **Verifiable Citations** | Inline `[1]`, `[2]` source tags in every response |
| **Confidence Scoring** | Retrieval similarity + faithfulness indicators |
| **LLM Backend** | DeepSeek V4 Flash (OpenAI-compatible API) |
| **Offline Mode** | Works without API key using rule-based fallback |
| **Zero External Installs** | Uses only pre-installed Python packages |

## Usage Examples

```python
# Query as an HR user — can access HR policies + general docs
result = query_with_rbac("Alice", "How many PTO days do senior employees get?")

# Query as a Finance user — can access financial data only
result = query_with_rbac("Bob", "What was Q3 2025 total revenue?")

# Query as a General Employee — restricted access
result = query_with_rbac("Charlie", "Show me the payroll discrepancy logs")
```

## Technology Stack

- **Vector Store:** ChromaDB (in-memory, built-in embeddings)
- **PDF Parsing:** PyMuPDF (fitz)
- **LLM:** DeepSeek V4 Flash via OpenAI-compatible endpoint
- **API Calls:** Raw `urllib` (no external HTTP library needed)
- **Language:** Python 3.10+

## Security Model

Access control is enforced **before** data reaches the LLM context window:

1. User submits query → identity resolved → roles fetched
2. ChromaDB `where` filter restricts results to `allowed_roles ∩ user_roles`
3. Only authorized chunks enter the LLM prompt
4. Unauthorized data is **never** seen by the model

This is a **pre-filtering** strategy, not post-filtering — meaning sensitive data cannot leak through prompt injection or model hallucination.

---

## License & Attribution

**© 2026 Abhay Gupta. All Rights Reserved.**

- **Author:** Abhay Gupta
- **GitHub:** [github.com/Abs6187](https://github.com/Abs6187)
- **LinkedIn:** [linkedin.com/in/abhay-gupta-197b17264](https://www.linkedin.com/in/abhay-gupta-197b17264/)
- **Twitter/X:** [x.com/AbhayGu19265651](https://x.com/AbhayGu19265651)

This project was built for the HackerEarth Simplify Challenge. Unauthorized reproduction or distribution is prohibited.
