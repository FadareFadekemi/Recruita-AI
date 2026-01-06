RecruitAI: Salesforce-Native Semantic Search Engine
An AI-powered recruiter tool that transforms static Salesforce candidate data into a searchable, intelligent talent pool using RAG (Retrieval-Augmented Generation). Built with FastAPI, Streamlit, and OpenAI, deployed on Render.

Key Features
Natural Language Search: Recruiters can ask complex questions like "Show me Java devs with B2B experience and high tenure" instead of using Boolean strings.

Persistent Vector Storage: Integrated with Supabase (pgvector). Uses Salesforce Record IDs as primary keys to ensure zero data duplication and efficient indexing.

Incremental Sync: A smart ingestion pipeline that only processes new or updated Salesforce records using SystemModstamp tracking to minimize API costs.

Explainable AI: Every result includes a plain-English explanation of the match and direct evidence snippets extracted from the resume.

One-Click Action: Direct, dynamic deep-links back to the Salesforce Contact record for immediate outreach.

🛠 Tech Stack
Language: Python 3.11+

Backend: FastAPI (Asynchronous API)

Frontend: Streamlit (Custom CSS-themed UI)

Database: Supabase + pgvector (Vector Search)

AI Models: * text-embedding-3-small (Embeddings)

gpt-4o (Reasoning & Explanation)

gpt-4o-mini (Structured Metadata Extraction)

Data Source: Salesforce Bulk API 2.0

Deployment: Render

📁 Project Structure
sf-recruiter-ai/
├── api/                   # FastAPI Backend & LLM Reasoning
├── ingestion/             # Salesforce Sync & PDF Processing
├── database/              # Supabase Client & SQL Schema
├── frontend/              # Streamlit User Interface
└── .env                   # Configuration (Local only)


⚙️ Setup & Installation
1. Database Setup
Execute the database/schema.sql in your Supabase SQL editor to enable pgvector and create the candidates table and the match_candidates function.

2. Environment Variables
Create a .env file in the root directory:

# Salesforce
SF_USERNAME=...
SF_PASSWORD=...
SF_SECURITY_TOKEN=...
SF_CLIENT_ID=...
SF_CLIENT_SECRET=...

# AI & DB
OPENAI_API_KEY=...
SUPABASE_URL=...
SUPABASE_SERVICE_ROLE_KEY=...


3. Running the Sync
To populate the database from Salesforce:

```python -m ingestion.sync_job```

4. Launching the App
```# Start Backend
uvicorn api.main:app --reload
# Start Frontend (New terminal)
streamlit run frontend/app.py```


System Design Choices
Why Supabase? It allows for a hybrid approach: storing relational metadata (like tenure) alongside vector embeddings, enabling "Hard Filtering" via SQL before performing "Soft Matching" via AI.

Cost Efficiency: Resumes are truncated before being sent to the LLM, and the Embedder uses batching to reduce network overhead and token waste during the initial backfill of 100k+ records.

Idempotency: The system uses the Salesforce Id as a unique constraint. Re-running the sync will never create duplicate entries; it only updates records that have changed.