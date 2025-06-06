# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Stock Performance Monitoring Web Application that allows users to manually monitor and log stock performance. The system scrapes stock prices from Tagesschau.de and stores them in a local SQLite database with a clean React frontend.

## Tech Stack

- **Frontend**: React with Tailwind CSS (Vite)
- **Backend**: Node.js with Express
- **Database**: SQLite with Prisma ORM
- **Web Scraping**: Puppeteer (headless Chrome)
- **Package Manager**: pnpm (workspace monorepo)
- **Testing**: Jest (unit), Playwright (E2E)
- **Deployment**: Local Docker containers

## Architecture

### Monorepo Structure
- `packages/backend/` - Express API server with Prisma
- `packages/frontend/` - React application with Vite
- Root workspace managed by pnpm

### Data Models
- **Stock**: id, name, ticker, country, url
- **PriceLog**: stockId (FK), date, time, price, currency
- **UserActionLog**: date, time, action, details, result

### Key Features
- Manual stock price scraping with 2-retry logic
- Comprehensive user action logging
- Responsive UI with dark/light mode toggle
- Data export to XLSX format
- Full-text search across all tables

## Development Commands

Once implemented, the project will use:

```bash
# Development
pnpm install          # Install dependencies
pnpm dev             # Start development servers
make docker-up       # Start Docker development stack
make docker-down     # Stop Docker containers

# Testing
pnpm test            # Run unit tests
pnpm test:e2e        # Run Playwright E2E tests
pnpm lint            # ESLint + Prettier
pnpm typecheck       # TypeScript compilation check

# Database
npm run db:migrate   # Run Prisma migrations
npm run db:seed      # Seed database with sample data
npm run db:backup    # SQLite backup script

# Scraping
npm run scrape -- --stockId=<id>  # Manual price scrape
```

## Implementation Guidelines

### Scraping Service
- Target URL: `https://www.tagesschau.de/wirtschaft/boersenkurse/`
- DOM selectors: `.ts-field--kurs` (price), `.ts-field--waehrung` (currency)
- Implement exponential backoff retry logic (max 2 retries, 250ms initial delay)
- Validate URL accessibility with HEAD request before saving stocks

### API Endpoints
- CRUD operations: `/stocks`, `/stocks/:id`
- Business logic: `/stocks/:id/scrape`, `/prices`, `/actions`
- Use Zod schemas for request validation
- Global error handling with `{error}` envelope format

### Frontend Components
- Use shadcn/ui component library
- Implement responsive design (mobile/tablet/desktop)
- Toast notifications for all user actions
- 100-item pagination for all data tables
- Dark/light mode persistence via cookies

### Testing Strategy
- Unit tests for utilities and data parsing
- Integration tests with in-memory SQLite
- E2E tests covering full user workflows
- Mock Puppeteer responses for scraper tests

### Logging Requirements
- Log every user action to UserActionLog table
- Include request details, timestamps, and outcomes
- Use Winston for file-based application logging
- Avoid logging sensitive information

## Development Phases

The project follows a 12-phase iterative development plan:
1. Project setup and tooling
2. Docker development environment
3. Prisma schema and migrations
4. Puppeteer scraping service
5. Express API with middleware
6. React frontend foundation
7. Stock CRUD interface
8. Price logging workflow
9. Evaluation charts and exports
10. Help section and FAQ
11. CI/CD and hardening
12. Documentation and deployment

## Key Constraints

- No authentication required (local-only application)
- English language only with consistent date/time formats
- SQLite for persistence (no external database)
- Manual backup strategy for database
- Browser support: modern browsers (Chrome, Firefox, Safari, Edge)