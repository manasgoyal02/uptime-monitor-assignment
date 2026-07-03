# AI Collaboration Log

This log chronicles the collaboration with AI to design, write, and refine the Uptime Monitoring MVP.

---

## 🛠️ The AI Tech Stack
*   **AI IDE Integration**: Antigravity Developer Suite.
*   **Underlying Large Language Models (LLMs)**: 
    *   **Claude 3.5 Sonnet** (for structural planning, architecture generation, and frontend vanilla CSS styling).
    *   **Gemini 3.5 Flash** (for task list synchronization, schema configurations, and debugging).

---

## 💬 The Prompts that Shipped It

Here are the raw prompts and specifications used to develop the core features:

### 1. Backend & Frontend Generation Prompt
> "Build a complete full-stack Uptime Monitoring MVP based on the following requirements:
> - Frontend: React + Vite
> - Backend: Node.js + Express
> - Database: PostgreSQL
> - ORM: Prisma
> - Scheduler: node-cron
> - HTTP requests: Axios
> - Containerization: Docker + Docker Compose
>
> Requirements:
> - POST /monitors (add new URL)
> - GET /monitors (list all URL)
> - GET /status (return latest UP/DOWN, status code, response time, checkedAt)
> - Cron checks every minute, measures response time, sets UP/DOWN on success/failure.
> - Dashboard form to add URL, table to show results. Polls status every 10s. Clean and beautiful Vanilla CSS."

### 2. Enhancements & Validation Prompt
> "Please include the following improvements in the implementation:
> 1. Immediate First Health Check: Run check immediately upon adding a monitor via POST /api/monitors, save it to DB, and then run node-cron every minute.
> 2. Loading & Error States: Show Checking... (yellow), UP (green), DOWN (red), Error (warning).
> 3. Health Check Retention: Keep only the latest 100 HealthCheck records per monitor.
> 4. Environment Variables: DATABASE_URL, PORT, CRON_SCHEDULE, REQUEST_TIMEOUT.
> 5. Enhanced Status API: GET /api/status returning clean JSON.
> 6. URL Validation: Validate on backend and reject duplicate/invalid URLs.
> 7. Docker Compose health check sequence."

---

## 🔄 The Course Corrections

During the project lifecycle, two major architectural adjustments were made based on environment conditions and developer needs:

### 1. Database Migration (PostgreSQL ──► MongoDB)
*   **Context**: The database requirement shifted mid-development to use MongoDB instead of SQL.
*   **The Prompt**: *"for backend use mongodb isntead"*
*   **The Issue**: Prisma's PostgreSQL configurations (migrations, autoincrementing integers, schema layout) were incompatible with Document-based structures. We updated `schema.prisma` to use the `mongodb` provider, changed relation schemas, and adjusted schemas to utilize string `ObjectID` properties mapped to `@map("_id")`.

### 2. ORM Transition (Prisma ──► Mongoose)
*   **Context**: Standard MongoDB servers run locally in standalone mode. However, Prisma Client requires MongoDB to be run as a **Replica Set** (even for single local instances) to support transactions. Attempting to connect a standard local server threw connection errors.
*   **The Prompt**: *"do not use prism use mongoose that can be easily integreated with mongodb"*
*   **The Refactor**: 
    1.  We uninstalled `@prisma/client` and `prisma` CLI.
    2.  Installed `mongoose` as the object data manager.
    3.  Created explicit model files for `Monitor` and `HealthCheck` in `backend/src/models/`.
    4.  Refactored `checker.js`, `scheduler.js`, and `monitorController.js` database queries to Mongoose syntax (using Mongoose aggregation, schema validations, and compound indexing).
    5.  Removed database replica set initiation requirements from `docker-compose.yml`, which simplified local setups.
