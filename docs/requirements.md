# CricScore: Requirements

## 1. Goal

Build a cricket scoring platform where visitors follow matches and scorecards, and admins and scorers manage data and record scores ball by ball.

## 2. Roles

| Role | Description |
|---|---|
| Visitor | Not logged in. Can read all public pages. |
| User | Logged in, self-registered. Same access as Visitor in v1 (room for favourites later). |
| Scorer | Can record balls for any match. Created by an Admin. |
| Admin | Manages all data and users. Also has all Scorer abilities. |

## 3. Use cases

### 3.1 Public (Visitor / User)
- UC-01 View live, upcoming and completed matches.
- UC-02 Open a match and view its scorecard (batting, bowling, extras, fall of wickets, result).
- UC-03 Browse teams and team squads.
- UC-04 Browse and search players.
- UC-05 Browse tournaments and see a simple points table.
- UC-06 Register and log in.

### 3.2 Admin
- UC-10 Create, edit and delete players.
- UC-11 Create, edit and delete teams.
- UC-12 Add or remove players in a squad and set the captain.
- UC-13 Create, edit and delete venues.
- UC-14 Create, edit and delete tournaments and add teams to them.
- UC-15 Create a match (teams, venue, date, overs, optional tournament).
- UC-16 Record the toss and start the match.
- UC-17 Manage users and assign roles.

### 3.3 Scorer
- UC-20 Start an innings and choose the openers and the bowler.
- UC-21 Record each ball: runs, extras, wicket.
- UC-22 Undo the last ball.
- UC-23 End the innings and complete the match.

## 4. Business rules

| ID | Rule |
|---|---|
| BR-01 | A team cannot play against itself. |
| BR-02 | A player appears only once in a team squad. A team has at most one captain. |
| BR-03 | A match needs two different teams, a venue, a date and a number of overs. |
| BR-04 | If a match belongs to a tournament, both teams must be in that tournament. |
| BR-05 | Match status moves Scheduled -> TossDone -> Live -> Completed; steps cannot be skipped. Allowed side paths: Scheduled <-> Postponed, Scheduled -> Cancelled. |
| BR-06 | An over has 6 legal balls. Wides and no-balls are not legal balls. |
| BR-07 | A completed match cannot be edited or scored. |
| BR-08 | Odd runs and the end of an over rotate the strike. |
| BR-09 | A wide adds 1 extra run plus runs taken; a no-ball adds 1 extra run and the next ball is a free hit. |
| BR-10 | Byes and leg byes count for the team and extras, not for the batter. |
| BR-11 | An innings ends when 10 wickets fall, the overs are finished, or the target is reached in the second innings. |
| BR-12 | Write endpoints require Admin (data) or Scorer/Admin (scoring). Authorization is enforced by the API, not only by Angular guards. |

## 5. Match lifecycle

```
Scheduled -> TossDone -> Live -> Completed
    |  ^
    v  |
 Postponed        Scheduled -> Cancelled
```

## 6. Decisions (Day 1)

| Topic | Decision |
|---|---|
| Match format | Limited-overs with a configurable number of overs (for example 20 or 50). No Test matches in v1. |
| Who can score | Any Scorer or Admin, for any match. |
| Registration | Users can self-register (User role only). Only an Admin can create Scorers. |
| Authentication | ASP.NET Core Identity with JWT. |
| Live scores | Polling every few seconds. SignalR is an optional extra at the end. |
| Data access | EF Core DbContext used directly from Application services. No generic Repository or Unit of Work. |
| Hosting | To be decided before Day 32. |

## 7. Out of scope (v1)

Test matches, DLS, super overs, retired batters, penalty runs, concussion substitutes, mobile app, microservices, Kafka/Redis, AI features, full cricket statistics.

## 8. API outline (v1)

| Area | Endpoints | Access |
|---|---|---|
| Auth | POST /api/v1/auth/login, /register, /refresh | Public |
| Players | GET (paged, search), GET {id}, POST, PUT {id}, DELETE {id} | Read public; write Admin |
| Teams | CRUD, POST/DELETE /teams/{id}/players | Read public; write Admin |
| Venues | CRUD | Read public; write Admin |
| Tournaments | CRUD, team assignment, GET {id}/points-table | Read public; write Admin |
| Matches | CRUD, GET live / upcoming / completed, POST {id}/toss, POST {id}/start | Read public; write Admin |
| Scoring | POST /matches/{id}/innings, POST /innings/{id}/balls, DELETE /innings/{id}/balls/last | Scorer, Admin |
| Scorecard | GET /matches/{id}/scorecard, GET /matches/{id}/live | Public |

## 9. Non-functional requirements

- All lists are paginated.
- One consistent error format (ProblemDetails with a trace id).
- Structured logging; no passwords, tokens or secrets in logs.
- Secrets are never committed to Git.
- Swagger/OpenAPI documents every endpoint.
