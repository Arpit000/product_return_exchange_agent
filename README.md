# Returns & Exchange Agent

A small LLM-powered customer support app for handling product returns and size exchanges in a ShopEasy-style ecommerce flow.

The project combines a conversational agent, a rules-driven returns workflow, a SQLite-backed order database, and retrieval over a knowledge base for policy and product information.

## What this project does

The app lets a customer:

- ask about their orders
- request a return or exchange
- get policy guidance from the handbook
- check product details from the catalog
- receive a guarded, rule-based response instead of unrestricted free-form product advice

The system enforces business rules before booking any action:

- only the logged-in customer can access their own orders
- a return or exchange cannot already be booked for the same order
- electronics cannot be returned or exchanged
- clothing and footwear can be exchanged only for an available in-stock size
- large refunds require staff approval

## Architecture

- `app.py` — Streamlit chat UI for customer conversations and staff review
- `agent.py` — LangGraph/LangChain agent setup and model configuration
- `tools.py` — Return/exchange tools, guardrails, and knowledge lookups
- `database.py` — SQLite schema, order data, and return workflow logic
- `retrieval.py` — RAG search over the handbook and product catalog
- `prompts.py` — System prompts for different agent versions
- `golden_dataset.py` — Rule-based evaluation examples for returns behavior
- `evals.py` — Runs the dataset against the agent and reports pass/fail results
- `knowledge/` — policy handbook and product catalog text used for retrieval
- `old/` — older prompt and dataset versions kept for comparison

## Tech stack

- Python
- Streamlit for the web UI
- LangChain / LangGraph for agent orchestration
- Groq-hosted model (`openai/gpt-oss-120b`)
- SQLite for order and return state
- Qdrant + FastEmbed for retrieval-augmented search
- Python dotenv for environment configuration

## Prerequisites

- Python 3.10+
- Access to a Groq API key
- Internet access for model and embedding downloads

## Setup

1. Create and activate a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate
```

2. Install dependencies:

```bash
pip install streamlit langchain langchain-groq langgraph python-dotenv qdrant-client fastembed
```

3. Add your Groq credentials in a `.env` file:

```env
GROQ_API_KEY=your_api_key_here
```

4. Initialize the SQLite sample data:

```bash
python database.py
```

This creates `shopeasy.db` with demo customers, orders, stock, and return records.

## Running the app

Start the Streamlit app:

```bash
streamlit run app.py
```

Open the local URL shown in the terminal, then:

- log in as a customer from the sidebar
- chat with the returns agent
- approve or reject pending staff cases from the sidebar

## How the agent works

The agent uses a simple but practical workflow:

1. The customer asks a return or exchange question.
2. The agent checks the logged-in customer's orders.
3. It searches the handbook or product catalog for specific policy details.
4. It applies guardrails before taking any action.
5. It books an approved return or exchange only when all conditions pass.
6. High-value refunds require manual staff approval.

## Retrieval and knowledge base

The project uses retrieval over two knowledge sources:

- `knowledge/handbook.txt` — ShopEasy policy and return rules
- `knowledge/catalog.txt` — product and category information

The retrieval layer combines semantic and keyword search, then reranks the best passages before passing them to the model.

## Evaluation

The project includes a rule-focused evaluation set to measure the agent's behavior.

Run the full evaluation set:

```bash
python evals.py
```

Test a specific version:

```bash
python evals.py v1
python evals.py v2
```

The default agent version is `v2` as defined in `prompts.py`.

## Notes

- This is a demo project meant to showcase an agent with guardrails and retrieval.
- The database is intentionally small and deterministic for testing.
- The product and policy text are designed to encode realistic return and exchange rules for the demonstration.

## License

This project is provided as a sample/demo project for learning and experimentation.
