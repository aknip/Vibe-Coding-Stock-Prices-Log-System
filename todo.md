## **0. 🚀 Project Kick-off**
- **Scope & Alignment**
    - Confirm feature list against specification
    - Choose repository name & licensing (MIT)

- **Repo Bootstrap**
    - Initialise **git** repository
    - Add .gitignore, .editorconfig, README.md, LICENSE
    - Configure **pnpm** workspace with packages/backend and packages/frontend

- **Quality Gates**
    - Add ESLint (Airbnb+TS), Prettier, Husky pre-commit hook
    - Add GitHub Issues template & PR template

---

## **1. 🛠️ Environment & Tooling**
- **Dockerised Dev Stack**
    - Dockerfile (multi-stage Node 20)
    - docker-compose.yml (api, db, frontend)
    - Bind-mount local source for live reload

- **Make/Task Scripts**
    - Makefile targets: dev, docker-up, docker-down, db-backup

- **Continuous Integration**
    - GitHub Actions workflow: lint → unit → integration → e2e

---

## **2. 🔗 Data Layer (Prisma → SQLite)**
- Define Stock model
- Define PriceLog model (FK → Stock)
- Define UserActionLog model
- Run initial migration & generate Prisma client
- Write DB seed script with sample stock
- Add unit tests for CRUD operations (in-memory SQLite)

---
## **3. 🤖 Scraper Service (Puppeteer)**
- Install Puppeteer & Cheerio
- Implement fetchPrice(url) with 2-retry exponential back-off
- Parse price + currency from Tagesschau DOM
- Provide CLI hook `npm run scrape -- --stockId=<id>`
- Integration tests (mock HTML & network errors)

---
## **4. 🌐 REST API (Express + Zod)**
- Core Server
    - Basic Express app & /health route

- **CRUD Routes**
    - GET /stocks
    - POST /stocks
    - PUT /stocks/:id
    - DELETE /stocks/:id

- **Business Routes**
    - POST /stocks/:id/scrape → triggers scraper
    - GET /prices (filterable by stockId)
    - GET /actions (user action logs)

- **Middleware**
    - Request logging to UserActionLog
    - Global error handler with envelope { error: string }

- Integration tests with Supertest

---
## **5. 🖥️ Frontend Foundation (Vite + React + Tailwind)**
- Initialise Vite React app in packages/frontend
- Configure Tailwind & shadcn/ui; add base theme tokens
- Implement `<Navbar>` with routes: Home, Stocks, Evaluation, Help
- Add dark/light toggle (cookie-based)
- Global Toast provider (top-right)
- Ping /health on load; show offline toast on failure

---

## **6. 📑 Stocks CRUD UI**
- `<StocksTable>` (react-table) with pagination (100 rows) & search
- `<StockFormModal>` with validation & URL HEAD check
- Row actions: **Edit**, **Delete**, **Get Price**
- Connect via React-Query mutations/queries
- Component tests (React Testing Library + MSW)

---

## **7. 💰 Price Logging Flow**
- Wire “Get Price” button → POST /stocks/:id/scrape
- Success toast shows price currency
- Optimistically update PriceLog cache
- Ensure UserActionLog entry saved
- Playwright E2E: create stock → scrape → assert toast

---

## **8. 📊 Evaluation & Export**
- **Home Dashboard**
    - Recharts **Line** chart for 30-day portfolio trend
    - Recharts **Pie** chart for current distribution

- **Raw Data Page**
    - Paginated table (100 rows) & sortable columns
    - Full-text search across all fields
    - **Download XLSX** via SheetJS

- Utility tests for data aggregation

---

## **9. ❓ Help & FAQ**
- Render static FAQ Markdown
- **User Actions Table** with search/sort & XLSX export
- Breadcrumbs & sub-navigation

---

## **10. 🛡️ Hardening & CI/CD**
- Add Playwright E2E for dark-mode persistence
- Configure GitHub Actions matrix (Node 20, OS)
- Build Docker images for api & frontend (Nginx static)
- Security audit (npm audit, Dependabot / Renovate)

---

## **11. 📦 Packaging & Release**
- Write release.sh for version tagging & image push
- Update CHANGELOG.md for v1.0.0
- Final README polish (setup, testing, backup, deployment)

---

## **12. 🧹 Maintenance & Ops**
- Create docs/DEPLOYMENT.md (local & prod)
- Document manual DB backup: sqlite3 dev.db .dump > backup.sql
- Schedule periodic cron reminder for backups (optional)
- Archive old logs ≥ 90 days automatically
---
> **Tip:** Work iteratively—complete each phase end-to-end (code → tests → docs) before moving on. Continuous integration must stay green at all times.
