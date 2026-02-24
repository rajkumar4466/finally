# FinAlly — AI Trading Workstation

AI-powered trading workstation with live market data, simulated portfolio, and an LLM assistant that can execute trades via natural language.

## Quick Start

```bash
cp .env.example .env        # add your OPENROUTER_API_KEY
docker build -t finally .
docker run -v finally-data:/app/db -p 8000:8000 --env-file .env finally
# Open http://localhost:8000
```

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| `OPENROUTER_API_KEY` | Yes | OpenRouter API key for AI chat |
| `MASSIVE_API_KEY` | No | Real market data (omit to use simulator) |
| `LLM_MOCK` | No | `true` for deterministic mock responses (testing) |

## Stack

Single container, port 8000 — Next.js static frontend, FastAPI backend, SQLite, SSE streaming, LiteLLM → OpenRouter (Cerebras).
