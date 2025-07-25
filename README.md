# OSCAR
ai assistant 
# OSCAR – Operational Strategist & Companion for Ambitious Resilience

> **Status:** v0.1 spec scaffolding – ready for initial development.

OSCAR is a local‑first AI assistant that captures your raw ideas, clarifies them, validates feasibility, and—when coding is required—hands off a structured **Build Brief** to a local Claude Code service.  Everything else (reasoning, planning, validation, memory) runs on OpenAI’s latest high‑reasoning model (`o3`).

---

## 1 · Quick Start (5 mins)

```bash
# 1 – clone repo
$ git clone https://github.com/steven4po/OSCAR.git
$ cd oscar

# 2 – environment
$ cp .env.example .env            # add OPENAI_API_KEY & CLAUDE_API_URL

# 3 – boot all services (Docker Desktop required)
$ docker compose up --build
```

Visit:

* **UI Dashboard:** [http://localhost:3000](http://localhost:3000)
* **Swagger / OpenAPI:** [http://localhost:8000/docs](http://localhost:8000/docs)
* **Postgres:** `localhost:5432` (oscar / oscar)

Shut down: `CTRL‑C` then `docker compose down`.

---

## 2 · Repository Layout

```
oscar-assistant/
├ backend/                 ← FastAPI + OSCAR agents
├ frontend/                ← Next.js 14 dashboard
├ mobile/                  ← (future Expo app)
├ briefs/                  ← OSCAR→Claude hand‑off
├ scripts/                 ← helper daemons & cron jobs
├ docs/                    ← all design docs (PRD, API spec, etc.)
├ docker-compose.yml       ← single‑command stack
├ .env.example             ← copy → .env and fill keys
└ README.md                ← you are here
```

> **Need details?** See `docs/ARCHITECTURE.md` for the full diagram.

---

## 3 · Tech Stack

| Layer      | Main Tech                                                  | Notes                               |
| ---------- | ---------------------------------------------------------- | ----------------------------------- |
| Frontend   | **Next.js 14**, Tailwind, shadcn/ui                        | Installs as PWA later.              |
| Backend    | **FastAPI**, SQLModel, pgvector                            | Async endpoints & agent logic.      |
| Database   | **Postgres 15 + pgvector**                                 | Vector search for memory.           |
| Background | **Celery + Redis**                                         | Brief watcher, nightly reflections. |
| AI Models  | **OpenAI o3** (reasoning) · **Claude Code** (local coding) | API keys in `.env`.                 |
| Containers | **Docker Compose**                                         | Mirrors prod env for dev parity.    |

---

## 4 · Common Commands

```bash
# run unit tests
$ docker compose exec backend pytest -q

# view logs from Celery worker
$ docker compose logs -f celery

# reset DB (danger!)
$ docker compose exec backend alembic downgrade base && alembic upgrade head
```

---

## 5 · Environment Variables (`.env`)

| Name                | Example                                                      | Description                      |
| ------------------- | ------------------------------------------------------------ | -------------------------------- |
| `OPENAI_API_KEY`    | sk‑\*\*\*\*                                                  | OpenAI o3 model key.             |
| `CLAUDE_API_URL`    | [http://localhost:5005/claude](http://localhost:5005/claude) | Local Claude Code REST endpoint. |
| `POSTGRES_PASSWORD` | oscar                                                        | DB password (compose uses same). |
| `REDIS_URL`         | redis\://redis:6379                                          | Celery broker / backend.         |

---

## 6 · Workflow

1. **Submit idea** via UI or `POST /idea`.  OSCAR runs the Clarifier Chain.
2. OSCAR scores feasibility; if coding needed ➜ generates `briefs/<slug>.md`.
3. `scripts/watch_briefs.py` detects new file and `POST`s payload to Claude.
4. Claude returns PR; OSCAR reviews, merges, updates dashboard.

---

## 7 · Contributing

All code is formatted with **black**, **ruff**, **isort**.  Run `pre‑commit install` after cloning.

1. Create feature branch: `feat/short‑slug`.
2. Ensure unit tests & linters pass.
3. PR must update relevant doc(s) in `docs/`.
4. One reviewer approval + CI green → merge.

---

## 8 · License

MIT – see `LICENSE` file.
