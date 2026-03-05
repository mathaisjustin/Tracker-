# Tracker — Homelab Installation & Validation Plan

This document is a **practical setup plan** to get your self-hosted Tracker system running in your homelab for smoke-tracking first, with support for more habits later.

---

## 0) Current Context / Important Notes

You mentioned the homelab environment is not fully ready yet:

- Database is not set up for this app yet
- Ollama models are not pulled yet
- Deployment target is your home server

This plan is written so you can execute setup in the right order and test end-to-end quickly.

---

## 1) Target Architecture (Services)

You will run these services via Docker Compose:

- `backend` (Node.js + Fastify + TypeScript)
- `dashboard` (SvelteKit + BetterAuth + Tailwind + shadcn-svelte)
- `telegram-bot` (grammY + parser workflow)
- `llm-parser` (local service calling Ollama)

And connect to an **existing local PostgreSQL** instance on your server.

---

## 2) What You Need To Prepare (Your Tasks)

### Task A — Database preparation (you must do this first)

1. Create a PostgreSQL database for Tracker (example name: `tracker`).
2. Create a dedicated DB user with strong password.
3. Grant only required privileges to that user.
4. Apply initial schema/migrations for:
   - `users`
   - `cigarette_brands`
   - `smoking_logs`
   - `habits`
   - `daily_summary` (optional aggregation table)
5. Verify external connectivity from Docker host using the `DATABASE_URL` you will use in containers.

Example connection string format:

```bash
DATABASE_URL=postgresql://tracker_user:your_password@192.168.1.50:5432/tracker
```

> Use your server LAN IP or DNS name in Linux environments (for example `192.168.1.50`), not `host.docker.internal` unless you explicitly configured `extra_hosts`/`host-gateway`.

> If PostgreSQL runs on the same host but outside Docker, ensure network rules allow container-to-host access.

---

### Task B — LLM runtime preparation (Ollama)

1. Install and start Ollama on the homelab machine.
2. Pull at least one model for parsing (recommended to pull 2 for fallback testing):
   - `mistral`
   - `llama3`
   - `phi`
3. Confirm Ollama API is reachable from containers.
4. Set `OLLAMA_URL` correctly (example: `http://192.168.1.50:11434`).

> Use the same Linux-safe host approach as `DATABASE_URL` (LAN IP/DNS or a Docker service name on a shared network).

Validation commands:

```bash
ollama list
curl http://localhost:11434/api/tags
```

---

### Task C — Telegram bot preparation

1. Create bot via BotFather.
2. Capture bot token and store securely.
3. Set webhook/polling mode as implemented (usually polling in homelab dev).
4. Set `TELEGRAM_BOT_TOKEN` in environment.

---

### Task D — Security and secrets

Generate and store:

- `BETTER_AUTH_SECRET` (strong random string)
- DB credentials
- Telegram token

Optional but recommended:

- `.env` managed through Docker secrets or a secure secret store
- reverse proxy + HTTPS for dashboard access

---

## 3) Suggested Build/Implementation Order

Follow this order exactly for least friction:

1. **Database schema + migration scripts**
2. **Backend API** (Fastify + Zod + auth guards + rate limiting)
3. **Telegram bot** (regex-first parser + LLM fallback)
4. **LLM parser service** (`POST /parse`, strict JSON response)
5. **Authentication integration** (BetterAuth + SvelteKit)
6. **Dashboard UI pages**
7. **Analytics engine/queries**
8. **Notification scheduler** (daily smoke-free message)

---

## 4) End-to-End Functional Goal (Smoke Flow)

You should be able to validate this sequence:

1. User sends Telegram message: `Marlboro 5`
2. Bot tries regex parser first
3. If regex parse fails, bot calls LLM parser
4. Parsed result is normalized and validated
5. Brand is matched/created based on policy
6. Log saved to `smoking_logs` with `source=telegram`
7. User receives confirmation message
8. Dashboard reflects updates in analytics

---

## 5) Parser Reliability Strategy (Important)

Implement this in Telegram bot pipeline:

1. **Regex parser first**
   - Supports patterns like:
     - `Marlboro 5`
     - `Marlboro 4 price 20`
2. **Fallback to LLM parser** only when regex fails
3. Validate parsed output with Zod before DB insert
4. Reject ambiguous/invalid payloads with user-friendly retry message

Benefits:

- lower latency
- fewer hallucinations
- lower homelab compute usage

---

## 6) Environment Variables Checklist

You will need at minimum:

```env
DATABASE_URL=
TELEGRAM_BOT_TOKEN=
OLLAMA_URL=
BETTER_AUTH_SECRET=
TIMEZONE=Asia/Kolkata
```

Optional additions:

```env
PORT_BACKEND=3001
PORT_DASHBOARD=3000
LOG_LEVEL=info
RATE_LIMIT_MAX=100
RATE_LIMIT_WINDOW_MS=60000
```

---

## 7) Docker Deployment Steps (When Code Is Ready)

1. Create `.env` with all required values.
2. Build and start services:

```bash
docker compose up -d --build
```

3. Check logs:

```bash
docker compose logs -f backend dashboard telegram-bot llm-parser
```

4. Validate service health:
   - dashboard loads
   - backend responds for authenticated calls
   - bot receives Telegram updates
   - parser returns strict JSON

---

## 8) Validation Checklist (Manual Test Plan)

### Authentication

- Register user from `/register`
- Login from `/login`
- Verify protected route access to `/dashboard`

### Telegram ingest

- `/start` works
- `/help` works
- `/link <code>` maps telegram account to user
- Message `Marlboro 5` creates DB log
- Message with price override parses and stores correctly

### Dashboard analytics

Confirm widgets show:

- daily cigarette count
- monthly consumption
- money spent
- brand breakdown
- smoke-free streak

Charts required:

- line chart (trend)
- bar chart (brand/period comparison)
- calendar heatmap (activity days)

### Notification job

- If no logs for a day → bot sends encouragement message
- If logs exist → no smoke-free message

---

## 9) Production Readiness Checklist

Before depending on the system daily:

- [ ] Backups enabled for PostgreSQL
- [ ] Non-root container users configured
- [ ] Healthchecks in compose
- [ ] Rate limits enforced on API
- [ ] Password hashing (bcrypt) verified
- [ ] JWT/session security validated
- [ ] Timezone behavior verified end-to-end
- [ ] Structured logging enabled
- [ ] Restart policies set in Docker

---

## 10) Quick “Do This Now” Action List

If you want the shortest path to first test, do these first:

1. Create DB + user + schema
2. Install/start Ollama and pull `mistral`
3. Create Telegram bot and get token
4. Set `.env` values
5. Start stack with Docker Compose
6. Send: `Marlboro 5`
7. Confirm DB row + dashboard update

---

If you want, next step I can create a **detailed execution tracker** (day-by-day checklist) with estimated time for each service build and testing block.
