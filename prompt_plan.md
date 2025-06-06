## **📐** 
## **Comprehensive Blueprint**
| **Phase**                    | **Goal**                      | **Deliverables**                                                             | **Key Concerns**                     |
| ---------------------------- | ----------------------------- | ---------------------------------------------------------------------------- | ------------------------------------ |
| **0. Project Kick-off**      | Align on scope & repo hygiene | README, mono-repo (pnpm), issue template, lint/format config                 | Naming, licensing                    |
| **1. Environment & Tooling** | One-command dev setup         | Docker docker-compose.yml, Node 20, SQLite DB volume, Playwright test runner | -                                    |
| **2. Data Layer**            | Durable persistence           | Prisma schema → SQLite, seed script, automated migrations                    | 1-to-many relations; ISO-dates       |
| **3. Scraper Service**       | Reliable price retrieval      | Puppeteer wrapper, retry logic, DTO validation                               | Tagesschau markup drift, rate-limits |
| **4. REST API**              | Secure & typed endpoints      | Express + Zod routes, Joi schemas, OpenAPI doc                               | CORS, error envelopes                |
| **5. Logging**               | Full audit trail              | UserAction middleware, Winston file log, DB log table                        | Sensitive data control               |
| **6. Frontend Foundation**   | Reusable UI kit               | Vite+React, Tailwind config, shadcn/ui setup, dark-mode toggle               | Accessibility                        |
| **7. Stocks CRUD**           | End-to-end data flow          | Table, popup form, optimistic mutation                                       | URL HEAD probe                       |
| **8. Price Fetch UI**        | Manual trigger + toasts       | “Get price” button, React-Query mutation, success/error toast                | Concurrency                          |
| **9. Evaluation Views**      | Insights & export             | Recharts line + pie, paginated raw table, xlsx export                        | Mobile layout                        |
| **10. Help & FAQ**           | Self-service docs             | Markdown FAQ renderer, User Action table view, xlsx export                   | Search UX                            |
| **11. Hardening**            | Robustness & polish           | E2E (Playwright), CI GitHub Actions, Docker prod compose                     | Test flaky scrapes                   |
|**12. Handoff**|Smooth ops|Detailed README, Makefile, backup script|Long-term maintainability|

---

## **🔄** 
## **Two-Level Iterative Breakdown**
### **Iteration A – Bootstrap**
1. Init mono-repo with pnpm, ESLint, Prettier
2. Add Dockerfile & docker-compose (Node, SQLite)
3. Write hello-world Express server + health check
4. Commit baseline Playwright test (expect HTTP 200)
### **Iteration B – Data Backbone**
1. Define Prisma schema for Stock, PriceLog, UserActionLog
2. Auto-generate TS types
3. Seed DB with sample stock
4. Add /stocks CRUD endpoints (no UI yet)
5. Unit tests for models & validators
### **Iteration C – Scraping Core**
1. Implement Puppeteer scraper module with retry(≤2)
2. Parse Tagesschau price & currency with cheerio
3. CLI script `npm run scrape -- --stockId=<id> for manual test`
4. Integration test (mock HTML)
### **Iteration D – Logging & Middleware**
1. Express middleware → insert UserActionLog for every mutation
2. Winston rotating-file transport
3. Verify log rows via test DB assertion
### **Iteration E – Frontend Skeleton**
1. Create Vite React app, Tailwind & shadcn/ui
2. Build Navbar (Home, Stocks, Evaluation, Help)
3. Add dark/light toggle (cookie)
4. Fetch /health on load → toast if backend down
### **Iteration F – Stocks CRUD UI**
1. Table component with search & pagination (React-Table)
2. Modal form, field validation, URL HEAD check
3. Connect to /stocks API with React-Query
4. Component tests (React Testing Library)
### **Iteration G – Price Logging Flow**
1. “Get price” button → POST /stocks/:id/scrape
2. Toast success/error, optimistic UI update
3. Ensure UserActionLog entry created
4. Playwright E2E: add stock → fetch price
### **Iteration H – Evaluation & Export**
1. Recharts line (last 30 days) + pie (distribution)
2. Raw Data table view with 100-row pagination
3. XLSX export using sheetjs
4. Visual regression test (storybook + percy)
### **Iteration I – Help Section**
1. Static FAQ markdown renderer
2. UserAction table view (+search, sort)
3. XLSX export hook reuse
### **Iteration J – Hardening & Docs**
1. Add CI workflow: lint, unit, integration, e2e
2. Docker prod compose with Nginx static + Node API
3. README: setup, tests, backup instructions
4. Tag v1.0.0 release

---
### **🔬** 
### **Second-Level Breakdown for Iteration B (sample)**
| **Step**                                                   | **Size** | **Safety Considerations**   |
| ---------------------------------------------------------- | -------- | --------------------------- |
| **B1.1** Create prisma/schema.prisma with Stock model only | S        | Covered by migration test   |
| **B1.2** Add PriceLog relation                             | S        | Foreign key constraint      |
| **B1.3** Add UserActionLog                                 | S        | None                        |
| **B1.4** Run prisma migrate dev                            | XS       | Auto-generated SQL reviewed |
| **B1.5** Write seed script (npm run db:seed)               | S        | Try/catch + rollback        |
| **B1.6** Unit test: seed inserts expected rows             | XS       | In-memory SQLite            |
(Every iteration gets a similar “atomic-step” table.)


---

## **✅** 
## **Right-Sizing Review**
- **Mean step time** ≤ 30 min 🔄 quick feedback
- **Each step** has: code, test, docs → no dangling work
- **Max blast radius**: DB schema changes isolated to single step then locked by tests
- **Parallelizable steps** (e.g., E2 dark-mode toggle vs. API health) explicitly noted in tasks list

---

## **🧑‍💻** 
## **LLM Code-Generation Prompt Series**
```
<details>
<summary>Why fenced <code>text</code> blocks?</summary>
Tools like GitHub Copilot Chat or GPT-4o can ingest these prompts verbatim without mis-interpreting markdown as code to execute.
</details>
```

### **Prompt 1 – Repo Initialization**

```
You are a coding agent.
Task: Initialize a mono-repo for a Node 20 + React + SQLite project.
Steps:
1. Create a Git repository with an MIT LICENSE and README describing the “Stock Performance Monitoring” app.
2. Configure **pnpm** workspace with two packages: `packages/backend` and `packages/frontend`.
3. Add root `.editorconfig`, `.prettierrc`, and `.eslintrc` (Airbnb + TS).
4. Provide a basic GitHub Actions workflow `.github/workflows/ci.yml` that lints and runs all tests with pnpm.
Write each file in full. Include commands in the README to run dev servers.
Add Jest placeholder tests so CI passes.
Return only file contents—no explanations.
```
---
### **Prompt 2 – Docker & Dev Tooling**

```
Continue from Prompt 1 (files exist).
Create:
- `Dockerfile` (multi-stage: deps cache, build, runtime Node 20-slim).
- `docker-compose.yml` with services:
    * `api`: mounts `packages/backend`, exposes 3000.
    * `db`: SQLite via `nouchka/sqlite3` image, volume `./db`.
    * `frontend`: mounts `packages/frontend`, uses `node:20`, port 5173.
Add `Makefile` targets: `docker-up`, `docker-down`, `docker-shell <svc>`.
Update README with Docker usage.
Write integration test in backend (`health.test.ts`) hitting `/health` while `docker-compose` is up (use fetch & env var for base URL).
Return complete file contents only.
```

---
### **Prompt 3 – Prisma Schema & Migrations**

```
Goal: Implement the data layer.
1. In `packages/backend`, add Prisma and set `DATABASE_URL` to `file:./dev.db`.
2. Define models:
   - `Stock` (id String@id @default(cuid()), name, ticker, country, url).
   - `PriceLog` (id, stockId FK, date, time, price Float, currency).
   - `UserActionLog` (id, date, time, action, details, result).
3. Run `npx prisma migrate dev --name init` and commit `prisma/migrations`.
4. Generate TS client and integrate into Express via `context.ts`.
Unit tests:
- `models.spec.ts` verifies CRUD on in-memory SQLite.
Provide only the new/changed files.
```

---
### **Prompt 4 – Scraper Module**

```
Implement a reusable scraper.
Requirements:
- Use Puppeteer (`npm i puppeteer-core @playwright/test -D`) in `packages/backend/src/scraper`.
- Function `async fetchPrice(url): Promise<{price: number, currency: string}>`.
- Retry up to 2 times on any error or missing DOM node; exponential backoff 250 ms.
- Throw descriptive error after final attempt.
- Extract price & currency from `https://www.tagesschau.de/wirtschaft/boersenkurse/` portfolio page DOM (selector `.ts-field--kurs` and `.ts-field--waehrung`).
Tests:
- Use Jest + `nock` to mock HTML responses; cover success & failure.
Return file diffs only.
```

---
### **Prompt 5 – Express Routes & Middleware**

```
Add routes:
- `GET /stocks`
- `POST /stocks`
- `PUT /stocks/:id`
- `DELETE /stocks/:id`
- `POST /stocks/:id/scrape` → calls scraper, inserts PriceLog, returns latest price.
- `GET /prices` with query `?stockId=`,
- `GET /actions`
Middleware:
- `logger` captures method, path, body, status; persists to UserActionLog.
- Global error handler returns `{error}` envelope.
Write Zod schemas for payload validation.
Tests:
- Integration tests for each route using Supertest + in-memory DB.
Return updated backend files only.
```

---
### **Prompt 6 – Frontend Scaffold**

```
Task: bootstrap React + Tailwind UI in `packages/frontend`.
1. Create Vite config with React-SWC, Tailwind, shadcn/ui setup script.
2. Add dark-mode toggle (class `dark`) stored in `cookie` `theme=dark|light`.
3. Build <Navbar /> with links Home, Stocks, Evaluation, Help. Active route highlighted.
4. Add `/health` fetch in `App.tsx` – if fail show Toast: "Backend offline".
Provide all files — no explanatory text.
```

---
### **Prompt 7 – Stocks Table & Modal**

```
Build Stocks CRUD UI.
Components:
- `<StocksTable />` using @tanstack/react-table with 100-row pagination and full-text search.
- `<StockFormModal />` (props: mode='create'|'edit', stock?).
   * Validates required fields; URL HEAD request before save.
- Buttons: Edit, Delete, Get Price.
- React-Query mutations to backend.
- On “Get Price”, show toast: "Current price: 123,00 EUR" or error.
Add component tests with React Testing Library & MSW to mock API.
Return new/updated frontend files only.
```

---
### **Prompt 8 – Charts & Evaluation**

```
Implement Evaluation page.
1. `/evaluation` route with subnav 'Raw Data'.
2. Home Page:
   - Line chart (Recharts) 30-day portfolio trend (sum of latest prices per day).
   - Pie chart distribution (latest price per stock).
3. Raw Data Table identical column set to backend `/prices`, 100 per page, sortable, search.
4. Add "Download XLSX" button (uses `xlsx` lib).
Unit tests for chart data utils.
Return diff only.
```

---
### **Prompt 9 – Help & User Actions**

```
Create two tabs under `/help`:
- FAQ: render `src/faq.md` via `react-markdown`.
- User Actions: table similar to Raw Data, source `/actions`, XLSX download.
Ensure breadcrumbs in Navbar.
Return only affected files.
```

---
### **Prompt 10 – CI + Release**

```
Finalize project.
1. Extend GitHub Actions:
   - Jobs: lint, unit, integration, e2e (Playwright headless), docker-build.
2. Add `Dockerfile.frontend` to build static assets, serve with Nginx.
3. Create root `release.sh` tags version and builds images.
Update README sections: Testing, Docker Prod, Backup (SQLite dump).
Return all changed files.
```

---
### **Prompt 11 – E2E Flow Verification**

```
Write Playwright script:
Scenario:
1. Run docker-compose up.
2. Browser flow:
   - Add stock “Apple Inc” ticker AAPL url `<tagesschau url>`.
   - Click “Get price” ➜ expect toast Success.
   - Navigate Evaluation ➜ verify pie slice label “Apple Inc”.
   - Switch theme to dark ➜ reload ➜ still dark.
Include test data teardown.
Commit to `e2e/stockFlow.spec.ts`.
```

---
### **Prompt 12 – Handoff Documentation**

```
Produce `docs/DEPLOYMENT.md`.
Contents:
- Local dev (pnpm ↔ Docker).
- Prod compose stack (frontend, api, db, nginx reverse proxy).
- Manual DB backup (`sqlite3 .dump > backup.sql`; schedule suggestion).
No code, just the markdown file.
```

---

**🟢 Usage**

Feed each prompt sequentially to your favorite code-gen LLM (e.g. gpt-4o-coder) and commit after every passing test suite. Each builds directly upon the repo produced by the previous prompt, ensuring safe, incremental progress and continuous verification.
