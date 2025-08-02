# ai2query – Explainable AI-Powered SQL Agent

**ai2query** is a modular, containerized, local-first AI agent that translates natural language into executable SQL queries, explains its logic, interprets PostgreSQL query plans, and self-corrects using Reflexion-style loops. Designed for secure, offline financial data analysis.

---

## Core Features

### 1. Natural Language → SQL

- Converts user queries into SQL using local LLMs via [Ollama](https://ollama.com).
- Injects real database schema (DDL) into prompt for context-aware generation.
- Validates table references and filters hallucinated queries.

### 2. SQL + Query Plan Explanation (XAI)

- Explains why specific tables, joins, or filters were used.
- Runs `EXPLAIN (FORMAT JSON)` on successful queries.
- Uses neuro-symbolic reasoning to describe performance characteristics and plan structure.

### 3. Reflexion Loop (Self-Healing)

- If SQL execution fails, captures the DB error.
- LLM retries up to 3 times, refining the query each time based on error messages.
- Logs recovery attempts and success metrics.

### 4. Model Evaluation & Analysis

- Benchmarks LLM output using:
  - Execution match vs reference queries
  - BLEU/ROUGE overlap scores
  - Reflexion recovery rate
  - Manual explanation scoring (optional)
- Test suite included with NL → SQL ground truth examples.

### 5. FastAPI Microservice

- Exposes REST endpoints:
  - `/connect`: inject schema
  - `/generate`: run query generation + explanation
  - `/logs/eval`: evaluation results
  - `/health/db`: DB healthcheck

### 6. Fully Containerized Deployment

Built with Docker Compose for isolated local development.

**Services:**

- `api`: FastAPI backend
- `ollama`: LLM (e.g., `sqlcoder:7b-q4_0`)
- `postgres`: financial data store
- `streamlit`: optional UI (chat-style interface)

---

## 🧱 Tech Stack

| Layer            | Technology              |
|------------------|--------------------------|
| Frontend UI      | Streamlit               |
| Backend API      | FastAPI                 |
| LLM              | Ollama (`sqlcoder:7b`)  |
| Database         | PostgreSQL              |
| ORM              | SQLAlchemy              |
| Evaluation       | BLEU, Execution Match   |
| Deployment       | Docker & Docker Compose |

---

## 🔌 API Endpoints

### `POST /connect`

Connects to a PostgreSQL instance and injects schema into the LLM prompt.

```json
{
  "host": "localhost",
  "port": 5432,
  "dbname": "finance_db",
  "user": "postgres",
  "password": "postgres"
}
```

### `POST /generate`

Generates, executes, and explains a SQL query from natural language.

```json
{
  "question": "Show total revenue by customer."
}
```

Returns:

```json
{
  "sql": "...",
  "preview": [...],
  "explanation": "...",
  "plan": "...",
  "attempts": 1,
  "recovered": false
}
```

### `GET /health/db`

Checks DB connection.

---

## Run Locally

### Prerequisites

- Docker

- Docker Compose


---

### 1. Clone the Repo

```bash
git clone https://github.com/yourname/ai2query.git
cd ai2query
```

---

### 2. Start All Services

```bash
docker-compose up --build
```

This will start:

- `api`: FastAPI backend

- `ollama`: LLM server (sqlcoder or phi)

- `postgres`: database with seed data

- `streamlit`: frontend


---

## Test Dataset (examples)

- `"Get top 5 customers by revenue"`

- `"Show average salary by department"`

- `"List accounts with negative balance"`


All test cases are located in:  
`/tests/test_cases.json`

---

## Project Structure

```
ai2query/
├── backend/
│   ├── routes/
│   ├── services/
│   ├── reflexion/
│   ├── explain/
│   └── evaluation/
├── db/
│   └── schema.sql
├── tests/
│   └── test_cases.json
├── prompts/
├── logs/
│   ├── reflexion_logs.json
│   └── eval_results.json
├── docker/
│   ├── Dockerfile.api
│   ├── Dockerfile.ui
│   └── docker-compose.yml
```

---

## License

MIT – use freely for educational or commercial purposes.

---
