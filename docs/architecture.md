# CricScore: Architecture

## Projects and references

| Project | References | Purpose |
|---|---|---|
| CricScore.Domain | nothing | Entities, enums, value objects, domain rules |
| CricScore.Application | Domain | Use cases, DTOs, validators, interfaces |
| CricScore.Infrastructure | Application, Domain | EF Core DbContext, identity, external services |
| CricScore.API | Application, Infrastructure | Controllers, middleware, `Program.cs` |
| CricScore.UnitTests | Domain, Application | Fast tests of rules and use cases |
| CricScore.IntegrationTests | API | End-to-end API tests (WebApplicationFactory) |
| CricScore.Web | none (HTTP only) | Angular UI |

```
Domain  <--  Application  <--  API
   ^             ^              |
   |             |              v
   +--------  Infrastructure  <-+   (API references it only to register services in Program.cs)
```

## Rules

- Domain references no other project and no framework packages (no EF Core, no ASP.NET Core).
- Application never references Infrastructure or API. It defines interfaces; Infrastructure implements them.
- Controllers only call Application services and return results. No business rules or database code in controllers.
- The API project references Infrastructure only at the composition root (`Program.cs` / extension methods) to wire up dependency injection.
- Shared compiler settings live in `Directory.Build.props` (net10.0, nullable enabled).

## Quick checks

```bash
dotnet list src/CricScore.Domain/CricScore.Domain.csproj reference        # must list nothing
dotnet list src/CricScore.Application/CricScore.Application.csproj reference   # Domain only
```
