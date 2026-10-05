# CricScore: ERD (draft)

Draft for Day 1. Columns are finalised on Day 4. Identity tables (Users, Roles, UserRoles) come from ASP.NET Core Identity and are not drawn here.

```mermaid
erDiagram
    Teams ||--o{ TeamPlayers : "has squad"
    Players ||--o{ TeamPlayers : "belongs to"
    Tournaments ||--o{ TournamentTeams : includes
    Teams ||--o{ TournamentTeams : "enters"
    Tournaments |o--o{ Matches : hosts
    Venues ||--o{ Matches : "played at"
    Teams ||--o{ Matches : "team A"
    Teams ||--o{ Matches : "team B"
    Matches ||--o{ Innings : has
    Innings ||--o{ BallEvents : records
    Players ||--o{ BallEvents : "batter / bowler"

    Players {
        int Id PK
        nvarchar FirstName
        nvarchar LastName
        date DateOfBirth
        int Role
        nvarchar BattingStyle
        nvarchar BowlingStyle
        datetime CreatedAt
        nvarchar CreatedBy
    }
    Teams {
        int Id PK
        nvarchar Name UK
        nvarchar ShortName
        datetime CreatedAt
    }
    TeamPlayers {
        int Id PK
        int TeamId FK
        int PlayerId FK
        datetime JoinedAt
        bit IsCaptain
    }
    Venues {
        int Id PK
        nvarchar Name
        nvarchar City
        nvarchar Country
    }
    Tournaments {
        int Id PK
        nvarchar Name
        nvarchar Format
        date StartDate
        date EndDate
        int Status
    }
    TournamentTeams {
        int TournamentId PK
        int TeamId PK
    }
    Matches {
        int Id PK
        int TournamentId FK "nullable"
        int TeamAId FK
        int TeamBId FK
        int VenueId FK
        datetime ScheduledAt
        int Status
        int Overs
        int TossWinnerTeamId FK "nullable"
        int TossDecision "nullable"
        int WinnerTeamId FK "nullable"
        nvarchar ResultText
        rowversion RowVersion
    }
    Innings {
        int Id PK
        int MatchId FK
        int Number
        int BattingTeamId FK
        int BowlingTeamId FK
        int Status
        int Runs
        int Wickets
        int Balls
        int Extras
        int Target "nullable"
        rowversion RowVersion
    }
    BallEvents {
        int Id PK
        int InningsId FK
        int OverNumber
        int BallInOver
        int BatterId FK
        int NonStrikerId FK
        int BowlerId FK
        int RunsOffBat
        int ExtraType
        int ExtraRuns
        bit IsLegalBall
        int DismissalType "nullable"
        int DismissedPlayerId FK "nullable"
        int FielderId FK "nullable"
        datetime CreatedAt
    }
```

## Constraints and indexes to add on Day 4

- Unique: `Teams.Name`, `TeamPlayers (TeamId, PlayerId)`, `Innings (MatchId, Number)`, `BallEvents (InningsId, OverNumber, BallInOver, sequence)`.
- Check: `Matches.TeamAId <> TeamBId`, `Matches.Overs > 0`.
- Indexes: all foreign keys, `Matches (Status, ScheduledAt)`.
- Batting and bowling figures are derived from `BallEvents`; there are no separate score tables.
