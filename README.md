# FinAlly — AI Trading Workstation

AI-powered trading workstation with live market data streaming, simulated portfolio trading, and an LLM chat assistant that can execute trades via natural language.

## Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js (static export), TypeScript, Tailwind CSS |
| Backend | FastAPI, Python, uv |
| Database | SQLite (lazy init, volume-mounted) |
| Real-time | Server-Sent Events (SSE) |
| AI | LiteLLM → OpenRouter (Cerebras) with structured outputs |
| Market data | GBM simulator (default) or Massive/Polygon.io API |

Single container, single port (8000). FastAPI serves both the API and the Next.js static export.

## Quick Start

```bash
cp .env.example .env
# Set OPENROUTER_API_KEY in .env

docker build -t finally .
docker run -v finally-data:/app/db -p 8000:8000 --env-file .env finally
# Open http://localhost:8000
```

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| `OPENROUTER_API_KEY` | Yes | OpenRouter API key for AI chat |
| `MASSIVE_API_KEY` | No | Polygon.io key for real market data; omit to use simulator |
| `LLM_MOCK` | No | Set `true` for deterministic mock responses (testing/CI) |

## API Reference

| Method | Path | Description |
|---|---|---|
| GET | `/api/stream/prices` | SSE stream of live price updates |
| GET | `/api/portfolio` | Positions, cash balance, total value, unrealized P&L |
| POST | `/api/portfolio/trade` | Execute trade: `{ticker, quantity, side}` |
| GET | `/api/portfolio/history` | Portfolio value snapshots (for P&L chart) |
| GET | `/api/watchlist` | Watchlist tickers with latest prices |
| POST | `/api/watchlist` | Add ticker: `{ticker}` |
| DELETE | `/api/watchlist/{ticker}` | Remove ticker |
| POST | `/api/chat` | Send message, get response + auto-executed actions |
| GET | `/api/health` | Health check |

## Project Structure

```
finally/
├── frontend/    # Next.js static export
├── backend/     # FastAPI uv project
│   ├── app/     # Application code
│   └── tests/   # Unit tests (pytest)
├── test/        # Playwright E2E tests + docker-compose.test.yml
├── planning/    # Project documentation
├── scripts/     # start/stop Docker helpers (Mac/Windows)
└── db/          # SQLite volume mount target (runtime)
```

## Development

**Backend:**
```bash
cd backend
uv sync --dev
uv run pytest              # run tests
uv run pytest --cov=app    # with coverage
uv run ruff check .        # lint
uv run ruff format .       # format
```

**E2E tests** (requires Docker):
```bash
cd test
docker compose -f docker-compose.test.yml up --abort-on-container-exit
```

## License

See [LICENSE](LICENSE).
