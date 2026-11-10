# InvestWise LK

**AI-powered investment advisory platform for Sri Lankan professionals**

InvestWise LK tracks a Sri Lankan professional's full position across ten instrument types, computes what they actually keep after tax, and answers *"where should this month's income go?"*

> IS 3920 · Individual Project on Business Solutions · University of Moratuwa · 2026–27

---

## The Problem

A professional earning in USD, living in Sri Lanka, faces two unanswered questions every month:

1. **When to convert?** — The LKR/USD rate moves enough that timing materially changes effective income.
2. **Where to invest?** — Savings accounts, fixed deposits, Treasury Bills, T-Bonds, unit trusts, debentures, CSE equities, gold — each with different rates, lock-ins, and *different tax treatments*. The advertised rate is not what you keep.

No existing tool prices Sri Lankan instruments in real, after-tax terms. InvestWise LK does.

---

## Architecture

A clean layered architecture with the dependency rule enforced in CI:

```
┌─────────────────────────────────────────┐
│  API Layer — FastAPI, Pydantic, SSE     │
├─────────────────────────────────────────┤
│  Service Layer — TaxEngine,             │
│  ComparisonEngine, PortfolioService,    │
│  InstrumentStrategy ×5, AdvisorService  │
├─────────────────────────────────────────┤
│  Repository Layer — SQLAlchemy 2.0      │
├─────────────────────────────────────────┤
│  PostgreSQL + Alembic migrations        │
└─────────────────────────────────────────┘
```

- **Domain models** (plain dataclasses) sit beside the service layer with no outward dependencies
- **Celery workers** handle nightly rate refresh, alerts, and monthly snapshots

---

## Modules

| # | Module | Effort | Description |
|---|--------|--------|-------------|
| 1 | Auth, Onboarding & Risk Profile | 4% | Clerk-managed auth, risk survey, income sources |
| 2 | Portfolio & Position | 14% | 10 instrument types via 5 strategies, liabilities, net worth |
| 3 | Tax & Comparison Engine | 15% | Effective-dated tax rules, after-tax real return comparison |
| 4 | FX Intelligence | 6% | LKR/USD trend, seasonality, conversion timing |
| 5 | AI Advisor | 25% | Single LangGraph agent with 10 typed tools |
| 6 | Goals, Alerts & Reporting | 4% | Compound projection, maturity alerts, monthly summaries |

---

## Instrument Types

Ten instrument types through five strategy implementations:

| Strategy | Instruments |
|----------|-------------|
| `InterestBearingDeposit` | Savings accounts, Fixed Deposits, Foreign currency FDs, Recurring Investment Plans |
| `DiscountInstrument` | Treasury Bills |
| `CouponSecurity` | Treasury Bonds, Corporate Debentures |
| `NavUnit` | Unit Trusts |
| `MarketPriced` | CSE equities, Physical gold |

---

## Tech Stack

### Backend
- **Python** · FastAPI · SQLAlchemy 2.0 · Alembic · Pydantic v2
- **PostgreSQL** — normalised schema with effective-dated tax rules
- **Celery + Redis** — scheduled background jobs
- **LangGraph** — single tool-calling agent
- **LangSmith** — tracing and evaluation

### Frontend
- **Next.js 15** · React · TypeScript
- **Tailwind CSS** · shadcn/ui · Radix
- **TanStack Query v5** — server state management
- **Recharts** — portfolio visualisations

### Infrastructure
- **Clerk** — managed authentication
- **Vercel** — frontend deployment
- **Railway** — API and workers
- **GitHub Actions** — CI/CD pipeline

---

## Project Structure

```
investwise-lk/
├── backend/
│   ├── app/
│   │   ├── api/          # FastAPI routers
│   │   ├── services/     # Business logic (tax, comparison, portfolio)
│   │   ├── repositories/ # Data access layer
│   │   ├── domain/       # Plain dataclasses
│   │   ├── agent/        # LangGraph agent + 10 tools
│   │   └── workers/      # Celery tasks
│   ├── migrations/       # Alembic
│   └── tests/
├── frontend/
│   ├── src/
│   │   ├── app/          # Next.js app router
│   │   ├── components/   # UI components
│   │   ├── hooks/        # TanStack Query hooks
│   │   └── lib/          # Utilities
│   └── tests/
├── docs/                 # ADRs, API docs, diagrams
└── .github/workflows/    # CI/CD
```

---

## Effort Split

- **65% Software Engineering** — architecture, schema, engines, testing, API design
- **25% AI** — single agent with 10 tools, memory, evaluation
- **10% Business Analysis** — interviews, SWOT, competitive analysis

---

## Evaluation Targets

| Metric | Target |
|--------|--------|
| Tax engine vs IRD worked examples | 100% pass rate |
| Tax engine line coverage | 95% (100% branch) |
| Agent factual accuracy (25-question set) | ≥ 85% |
| Tool-selection correctness | ≥ 90% |
| Time to investment decision | < 90s (vs 30min manual) |
| System Usability Scale | ≥ 70 |
| p95 API latency | < 300ms |
| Celery job success rate (30 days) | ≥ 99% |

---

## License

This project is developed as part of the IS 3920 Individual Project module at the University of Moratuwa.
