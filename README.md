# HealthyIT

A healthcare IT ticketing API that prioritizes support tickets by **patient care impact**, not just by who asked loudest. It also monitors the health of clinical systems and opens tickets automatically when they go down.

> **Synthetic data only.** This project contains no real patient information (PHI), and none should ever be entered into it.

## Why this exists

In a hospital, a down workstation in the ER matters more than one at the front desk. HealthyIT makes that explicit: every ticket carries an impact level, which drives its priority and its response-time (SLA) clock, so the issues most likely to delay patient care surface first.

## How prioritization works

| Impact | Example | Target response | Base priority |
|---|---|---|---|
| Critical | ER workstation down, medication cart offline | 15 min | P1 |
| High | Slow EHR, label printer down | 60 min | P2 |
| Medium | Billing workstation issue | 4 hrs | P3 |
| Low | Monitor request | 24 hrs | P4 |

Tickets from high-criticality departments (ER, ICU, pharmacy) are bumped up one priority level, capped at P1.

## Features

- [ ] Create, view, update, and close tickets
- [ ] Automatic priority and SLA due-time calculation
- [ ] Reports: tickets at risk of breaching SLA, open tickets by impact, average resolution time by department
- [ ] Audit trail of every ticket change
- [ ] Service health checker that auto-creates Critical tickets when a monitored system goes down
- [ ] Docker container
- [ ] Deployed to Azure with Terraform
- [ ] CI/CD with GitHub Actions

## Tech stack

- **Python** with **FastAPI** and **SQLModel**
- **SQLite** for local development, planned move to **PostgreSQL** for deployment
- **pytest** for tests
- **Docker**, **Terraform**, **GitHub Actions**, **Azure** (planned)

## Getting started

Requires Python 3.12+.

```powershell
git clone https://github.com/YOUR-USERNAME/healthyit.git
cd healthyit
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn app.main:app --reload
```

Then open:
- `http://127.0.0.1:8000` for the health check
- `http://127.0.0.1:8000/docs` for interactive API documentation

## Running tests

```powershell
pytest
```

## Project structure

```
app/
  main.py          # app entry point
  models.py        # database tables
  database.py      # database connection
  sla.py           # priority and SLA logic
  routers/         # API endpoints
health_checker/    # separate service monitor (planned)
mock_service/      # simulated clinical system for testing (planned)
tests/
docs/decisions.md  # design decisions and the reasoning behind them
```

## Design decisions

Key choices (why FastAPI, why a separate health checker, why an append-only audit table, and so on) are documented in [docs/decisions.md](docs/decisions.md).

## Security and privacy

- Synthetic data only, and no PHI in tickets, descriptions, or logs
- Configuration through environment variables, with no secrets in the repo
- All timestamps stored in UTC
- **In a production deployment, this would also need:** authentication and role-based access control, encryption in transit and at rest, retention policies for audit logs, and a compliance review (such as HIPAA requirements)

## Roadmap

- Business-hours SLA calculation
- Configurable SLA rules per organization
- Link tickets to devices (asset tracking)
- Dashboard page
- Email or Teams-style alert simulation