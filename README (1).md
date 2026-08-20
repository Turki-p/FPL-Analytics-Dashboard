# ⚽ FPL-Analytics-Dashboard

An interactive Power BI dashboard built from scratch, pulling live data directly from the official Fantasy Premier League (FPL) API to analyze player performance, team fixtures, and fantasy value across the Premier League.

![Theme](https://img.shields.io/badge/Power%20BI-FPL%20Themed-38003C?style=flat-square)
![DAX](https://img.shields.io/badge/DAX-Advanced-00FF85?style=flat-square)
![Data Source](https://img.shields.io/badge/Data-FPL%20API-E90052?style=flat-square)

## 📊 Overview

Fantasy Premier League (FPL) is the official fantasy football game of the Premier League, where managers pick a squad of real players and earn points based on their actual on-pitch performance (goals, assists, clean sheets, bonus points, etc.).

This project combines a genuine passion for the game with hands-on Power BI development — pulling live data straight from FPL's public API, modeling it into a proper star schema, and building a fully interactive 4-page dashboard styled with the Premier League's official brand colors.

## 🗂️ Pages

| Page | What it shows |
|---|---|
| **Overall** | League-wide summary — total players/teams, top scorers, points per game leaders, active player distribution by position |
| **Player Explorer** | Full searchable player database with photos, price/team/position filters, and sortable stats |
| **Position** | Position-level breakdown — average points per game, bonus point distribution, total points contribution by position |
| **Fixtures** | Interactive Fixture Difficulty Ticker showing each team's opponent, home/away status, and color-coded difficulty rating, with a gameweek range slicer |

## 🛠️ Tech Stack

- **Power BI Desktop** — modeling, DAX, report design
- **Power Query (M)** — API ingestion and transformation
- **FPL Official API** — [fantasy.premierleague.com/api](https://fantasy.premierleague.com/api/bootstrap-static/)
- **DAX** — including `USERELATIONSHIP`, `LOOKUPVALUE`, and dynamic date-context measures

## 🔌 Data Source

All data is pulled live from FPL's public, no-auth-required API endpoints:

- `bootstrap-static/` — players, teams, gameweeks, positions
- `fixtures/` — full fixture list with difficulty ratings

## 🧠 Data Model

A proper star schema with 5 tables:

- **Fact tables:** `Players`, `Fixtures`
- **Dimension tables:** `Teams`, `Positions`, `Events` (gameweeks)

Relationships include a dual home/away link between `Fixtures` and `Teams`, resolved dynamically in DAX via `USERELATIONSHIP` for the away-team direction.

## ✨ Notable DAX

The Fixture Difficulty Ticker resolves each team's actual opponent (not just a raw difficulty number) using a combination of `USERELATIONSHIP` and `LOOKUPVALUE`:

```dax
Fixture Display = 
VAR HomeDiff = CALCULATE(MAX(Fixtures[team_h_difficulty]), USERELATIONSHIP(Fixtures[team_h], Teams[id]))
VAR AwayDiff = CALCULATE(MAX(Fixtures[team_a_difficulty]), USERELATIONSHIP(Fixtures[team_a], Teams[id]))
VAR IsHome = NOT ISBLANK(HomeDiff)
VAR OpponentID = IF(IsHome, CALCULATE(MAX(Fixtures[team_a]), USERELATIONSHIP(Fixtures[team_h], Teams[id])), CALCULATE(MAX(Fixtures[team_h]), USERELATIONSHIP(Fixtures[team_a], Teams[id])))
VAR OpponentShort = LOOKUPVALUE(Teams[short_name], Teams[id], OpponentID)
VAR HA = IF(IsHome, "(H)", "(A)")
RETURN OpponentShort & " " & HA
```

## 📸 Screenshots

<img width="1311" height="826" alt="image" src="https://github.com/user-attachments/assets/b17e30fa-3f63-4f25-b75a-5d9a5f371c5d" />
<img width="1321" height="835" alt="image" src="https://github.com/user-attachments/assets/08d5049c-1023-43a2-ac7c-29b360f5bd9e" />
<img width="1321" height="841" alt="image" src="https://github.com/user-attachments/assets/b46eb6ae-292e-453d-b77d-7c66d0352e51" />
<img width="1312" height="840" alt="image" src="https://github.com/user-attachments/assets/11e3d3f4-035d-4efb-a432-61dfd72df3c9" />
<img width="1289" height="786" alt="image" src="https://github.com/user-attachments/assets/4e6f8ea8-6916-4644-9854-0837e273fbfa" />






## 📝 Notes

- Squad, prices, and team data reflect the 2026/27 season (newly promoted clubs, current player pool), while performance stats capture the completed 2025/26 season (38 gameweeks) — captured as a snapshot before the new season reset these figures to zero.
- Player headshots are pulled dynamically from Premier League's official media CDN using each player's unique `code` field.

## 🚀 How to Use

1. Download `FPL_Dashboard.pbix`
2. Open in Power BI Desktop
3. If you want live current-season data instead of the frozen snapshot, refresh the queries (Home > Refresh) — note this will overwrite the archived 2025/26 stats

## 📄 License

Data is sourced from the official, publicly accessible FPL API. This project is for portfolio/educational purposes.
