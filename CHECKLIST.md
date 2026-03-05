# Tracker Homelab Execution Checklist

Use this as your working tracker. Mark each task as you complete it.

---

## 1) Pre-Setup (Homelab Readiness)

- [ ] Confirm server has Docker + Docker Compose installed.
- [ ] Confirm PostgreSQL is reachable from the Docker host.
- [ ] Confirm you have shell access with permissions to run containers.
- [ ] Decide and set deployment timezone (example: `Asia/Kolkata`).

---

## 2) Database Setup (Do First)

### 2.1 Create DB + User
- [ ] Create database `tracker` (or your preferred name).
- [ ] Create dedicated DB user (example: `tracker_user`).
- [ ] Set a strong password for DB user.
- [ ] Grant least-privilege permissions on tracker DB.

### 2.2 Core Schema
- [ ] Create table `users`:
  - [ ] `id`
  - [ ] `email`
  - [ ] `password_hash`
  - [ ] `telegram_id`
  - [ ] `created_at`
- [ ] Create table `cigarette_brands`:
  - [ ] `id`
  - [ ] `name`
  - [ ] `default_price_per_pack`
  - [ ] `cigarettes_per_pack`
  - [ ] `created_at`
- [ ] Create table `smoking_logs`:
  - [ ] `id`
  - [ ] `user_id`
  - [ ] `brand_id`
  - [ ] `quantity`
  - [ ] `price_override`
  - [ ] `date`
  - [ ] `source` (`telegram` / `web` / `manual`)
  - [ ] `created_at`
- [ ] Create table `habits` (future-proof):
  - [ ] `id`
  - [ ] `name`
  - [ ] `unit`
  - [ ] `type`
- [ ] (Optional) Create table `daily_summary`:
  - [ ] `id`
  - [ ] `user_id`
  - [ ] `date`
  - [ ] `total_cigarettes`
  - [ ] `total_spent`

### 2.3 DB Validation
- [ ] Verify app connection string works from host.
- [ ] Verify app connection string works from container network.
- [ ] Save final `DATABASE_URL` for `.env`.

---

## 3) LLM Setup (Ollama)

- [ ] Install Ollama on homelab machine.
- [ ] Start Ollama service.
- [ ] Pull at least one model (`mistral` recommended).
- [ ] Pull one backup model (`llama3` or `phi`).
- [ ] Verify API endpoint is reachable.
- [ ] Save final `OLLAMA_URL` for `.env`.

Suggested checks:
- [ ] `ollama list`
- [ ] `curl http://localhost:11434/api/tags`

---

## 4) Telegram Bot Setup

- [ ] Create Telegram bot using BotFather.
- [ ] Save `TELEGRAM_BOT_TOKEN` securely.
- [ ] Decide polling vs webhook mode.
- [ ] If webhook mode: configure public URL + TLS.
- [ ] Test `/start` response.
- [ ] Test `/help` response.

---

## 5) Secrets & Environment

### Required
- [ ] `DATABASE_URL`
- [ ] `TELEGRAM_BOT_TOKEN`
- [ ] `OLLAMA_URL`
- [ ] `BETTER_AUTH_SECRET`
- [ ] `TIMEZONE`

### Optional but Recommended
- [ ] `PORT_BACKEND`
- [ ] `PORT_DASHBOARD`
- [ ] `LOG_LEVEL`
- [ ] `RATE_LIMIT_MAX`
- [ ] `RATE_LIMIT_WINDOW_MS`

### Security
- [ ] Generate a strong `BETTER_AUTH_SECRET`.
- [ ] Store secrets outside git.
- [ ] Restrict `.env` file permissions.

---

## 6) Build Order Checklist (Development Sequence)

- [ ] 1. Database schema + migrations complete.
- [ ] 2. Backend API complete (Fastify + TypeScript + Zod + auth + rate limit).
- [ ] 3. Telegram bot complete (grammY).
- [ ] 4. LLM parser service complete (`POST /parse`).
- [ ] 5. BetterAuth integrated in SvelteKit dashboard.
- [ ] 6. Dashboard pages done:
  - [ ] `/login`
  - [ ] `/register`
  - [ ] `/dashboard`
  - [ ] `/dashboard/smoking`
  - [ ] `/dashboard/reports`
- [ ] 7. Analytics implementation complete.
- [ ] 8. Notification scheduler complete.

---

## 7) Parsing Strategy Checklist (Important)

- [ ] Implement regex parser first in Telegram ingestion flow.
- [ ] Regex supports `Brand Quantity` format (e.g., `Marlboro 5`).
- [ ] Regex supports optional price format (e.g., `Marlboro 4 price 20`).
- [ ] Fallback to LLM parser only when regex fails.
- [ ] Validate parsed output with Zod before DB insert.
- [ ] Reject malformed messages with user-friendly error.

---

## 8) Docker Deployment Checklist

- [ ] Create/update `docker-compose.yml` with:
  - [ ] `backend`
  - [ ] `dashboard`
  - [ ] `telegram-bot`
  - [ ] `llm-parser`
- [ ] Ensure all services read required env values.
- [ ] Ensure services can access external PostgreSQL.
- [ ] Build and run stack: `docker compose up -d --build`.
- [ ] Verify container health and restart behavior.
- [ ] Review logs for all services.

---

## 9) Functional Testing Checklist (End-to-End)

### Auth
- [ ] Register user from `/register`.
- [ ] Login user from `/login`.
- [ ] Verify protected routes require auth.

### Telegram + Ingestion
- [ ] Run `/start` command successfully.
- [ ] Run `/help` command successfully.
- [ ] Run `/link <code>` successfully and map Telegram user.
- [ ] Send `Marlboro 5` and verify log insert.
- [ ] Send `Marlboro 4 price 20` and verify price override insert.

### Dashboard Analytics
- [ ] Daily cigarette count renders.
- [ ] Weekly trend renders.
- [ ] Monthly consumption renders.
- [ ] Money spent renders.
- [ ] Brand breakdown renders.
- [ ] Smoke-free streak renders.

### Charts
- [ ] Line chart works.
- [ ] Bar chart works.
- [ ] Calendar heatmap works.

### Notifications
- [ ] Daily scheduler runs.
- [ ] If no logs for day, message is sent.
- [ ] If logs exist, no smoke-free notification is sent.

---

## 10) Production Readiness Checklist

- [ ] Password hashing uses bcrypt.
- [ ] JWT/session security validated (BetterAuth).
- [ ] API input validation in place (Zod everywhere).
- [ ] API rate limits active.
- [ ] Structured logging enabled.
- [ ] Healthchecks defined for services.
- [ ] Non-root container users configured.
- [ ] DB backup plan implemented and tested.
- [ ] Timezone behavior verified across API, bot, and dashboard.

---

## 11) Quick First-Test Path (Shortest Route)

- [ ] Create DB + user + schema.
- [ ] Install/start Ollama + pull `mistral`.
- [ ] Create Telegram bot + add token.
- [ ] Configure `.env`.
- [ ] Start stack with Docker Compose.
- [ ] Send `Marlboro 5` in Telegram.
- [ ] Confirm row in `smoking_logs`.
- [ ] Confirm dashboard reflects latest data.

---

## 12) Personal Notes / Blockers

### Blockers
- [ ] Blocker 1:
- [ ] Blocker 2:
- [ ] Blocker 3:

### Decisions
- [ ] DB host decision:
- [ ] Ollama model decision:
- [ ] Polling vs webhook decision:

### Next Actions
- [ ] Next action 1:
- [ ] Next action 2:
- [ ] Next action 3:
