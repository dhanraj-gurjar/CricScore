# CricScore

A cricket scoring platform: public match centre (live, upcoming, completed matches, scorecards, teams, players, tournaments) plus an admin and scorer area for managing data and recording ball-by-ball scores.

> **Status:** Planning, Day 1 of 35. See [docs/progress.md](docs/progress.md).

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | Angular, TypeScript, RxJS, Reactive Forms |
| Backend | ASP.NET Core Web API, C#, EF Core |
| Database | SQL Server |
| Auth | ASP.NET Core Identity + JWT (roles: Admin, Scorer, User) |
| Testing | xUnit, WebApplicationFactory (backend); Jasmine/Karma (Angular) |
| CI/CD | GitHub Actions |

## Architecture

Clean Architecture style layering: `API -> Application -> Domain`, with `Infrastructure` depending on `Application` and `Domain`. See [docs/requirements.md](docs/requirements.md) and [docs/erd.md](docs/erd.md).

## Repository layout

```
CricScore/
|-- src/          API, Application, Domain, Infrastructure, Web (Angular)
|-- tests/        UnitTests, IntegrationTests
|-- database/     seed scripts and notes
|-- docs/         requirements, ERD, architecture, ADRs
`-- README.md
```

## Getting started

Setup instructions will be added as the project is built (Day 34).

## Roadmap

- **M1 (Days 1-15):** auth, players, teams, venues, matches, Angular lists and admin forms
- **M2 (Days 16-27):** scoring engine, scorecard, live updates, scorer UI
- **M3 (Days 28-35):** tournaments, hardening, CI, deployment, documentation

## License

MIT
