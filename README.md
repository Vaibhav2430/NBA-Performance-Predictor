# NBA & WNBA Stat Predictor

AI-powered predictions for **Points**, **Assists**, and **Rebounds** for any NBA or WNBA player's next game, using XGBoost models trained on real game logs.

## Stack

- **Backend** – FastAPI + XGBoost + nba_api + ESPN API
- **Frontend** – React + Vite + Recharts

## Features

- NBA and WNBA support with a league toggle
- Player search with live autocomplete dropdown (active players only)
- Per-game predictions with floor/ceiling range
- Today's live scoreboard in the sidebar with team logos, scores, records, and game times
- Player banner showing team logo, full team name, and offensive/defensive ranking badges (color-coded by tier)
- Game log table with opponent team logos and their offensive/defensive rankings
- Previous season data fallback when a player has limited current-season games
- Prediction cap based on current season average + standard deviation to prevent overestimates

## How it works

1. Fetches the player's current season game log (falls back to previous season if fewer than 15 games)
2. Engineers 17 features per game:
   - Rolling L5/L10 averages for PTS, AST, REB, MIN
   - Home/away splits (player's actual home vs away averages)
   - Days rest
   - Opponent defensive rating and pace (NBA) / points allowed per game (WNBA)
3. Trains three XGBoost regressors (one per stat) with recent games weighted 2.25× more than older ones
4. Caps the prediction at `season_avg + 1 standard deviation` using current season data only

## Ranking colors

Rank badges use dynamic thirds based on league size (30 NBA teams, 15 WNBA teams):
- **Green** – top third (elite)
- **Orange** – middle third
- **Red** – bottom third

## Run locally

```bash
./start.sh
```

This kills any existing processes on ports 8000 and 5173, starts the backend and frontend together, waits for the backend to be ready, then opens the app in your browser.

Or run manually:

```bash
# Backend
cd backend
uvicorn main:app --port 8000

# Frontend (separate terminal)
cd frontend
npm run dev
```

Open [http://localhost:5173](http://localhost:5173), click the search box, select a player, and hit **Predict**.

> NBA predictions take ~10s. WNBA predictions take ~15s (ESPN API + model training). Results are not cached between sessions.

## API

```
GET /predict?player=LeBron James
GET /search?q=lebron

GET /wnba/predict?player=Caitlin Clark
GET /wnba/search?q=clark

GET /games/today
GET /wnba/games/today
```
