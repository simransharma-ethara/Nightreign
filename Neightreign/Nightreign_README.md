# NIGHTREIGN — Dark Battle Arena Gaming Platform

> *"In the Nightreign, there are no kings. Only survivors."*

Nightreign is a fully-featured, dark-mythology-themed competitive online gaming platform. Players duel in real-time cursed arenas, rise through five shadow-ranked tiers from **Shade** to the coveted **Nightreign** crown, forge covenants, and carve their name onto an eternal leaderboard. Built as a complete full-stack web development showcase — runs entirely on your local machine.

---

## Quick Start

```bash
# 1. Clone the repository
git clone <repo-url> nightreign
cd nightreign

# 2. Install all dependencies (frontend + backend)
npm install

# 3. Set up the database and seed 20 dark-fantasy starter players
npm run db:setup

# 4. Start the development server
npm run dev
```

The platform opens at **http://localhost:5173**
The API + WebSocket server runs at **http://localhost:3000**

> **No paid service. No API key. No signup. Fully offline after `npm install`.**

---

## Testing the Full Loop

### Step 1 — Register Two Accounts

Open **two browser tabs** at `http://localhost:5173`

- Tab 1: Register as `VoidWalker` / `void@rift.gg`
- Tab 2: Register as `AshenBlade` / `ash@rift.gg`

### Step 2 — Enter the Rift (Matchmaking)

In both tabs: click **Enter the Rift** → **Join Queue**

The matchmaking server auto-pairs players in the same rank band → both tabs enter **Shadowrift Duel**.

### Step 3 — Play the Duel

| Key | Action |
|-----|--------|
| W / ↑ | Move up |
| S / ↓ | Move down |
| A / ← | Move left |
| D / → | Move right |
| Space | Strike |
| Shift | Dodge |
| Esc | Pause / quit |

**Win condition:** Deplete the opponent's HP to zero, or outscore them when the 3-minute timer expires.

### Step 4 — Check the Results

After the match ends:
- Both tabs show the **animated post-match screen** (XP earned, rank tier change, top stat)
- Navigate to **Leaderboard** — both players' stats update live
- Navigate to your **Profile** — XP bar, match history, and rank tier all reflect the result

### Step 5 — Spectate a Live Match

1. Start a match in two tabs
2. Open a **third tab** at `http://localhost:5173`
3. Go to **Leaderboard → Live Duels**
4. Click **Watch** on the active match
5. Spectator view updates with ≤1.5s delay — HP bars, score, and timer all live

---

## Verifying All Controls & Features

| Feature | How to Verify |
|---------|--------------|
| Auth | Register → logout → login → session survives page reload |
| Matchmaking | Two tabs → Join Queue → both enter duel automatically |
| Game controls | WASD moves, Space strikes, Shift dodges — all responsive |
| Win condition | Reduce opponent HP to 0 or outlast timer — post-match screen fires |
| Post-match animation | Screen slides up — XP, rank delta, and rematch button visible |
| Leaderboard sort | Click column headers (Rank Points, Win Rate, Total Duels) — rows reorder |
| Leaderboard search | Type a username in the search box — rows filter live |
| Profile update | Play a match → visit Profile → XP bar and match history updated |
| Covenant create | Settings → Covenants → Create → fill name, tag, emblem → save |
| Covenant join | Second tab → Covenants → search → Join → both see membership |
| Rift chat | Type in lobby chat → message appears in both tabs without reload |
| In-duel chat | Type in duel chat sidebar → opponent sees it in their HUD |
| Spectator mode | Third tab → Live Duels → Watch → HP/score updates with ~1.5s delay |
| Stat dashboard | Profile → Stats → two charts render with your real match data |
| Config hot-reload | Edit `data/game-config.json` (e.g. `"scoreToWin": 5`) → reload → win at 5 |
| Methodology panel | Leaderboard → How Stats Work → formula and seed policy visible |

---

## Project Structure

```
nightreign/
├── client/                     # React 18 + Vite frontend
│   ├── src/
│   │   ├── components/         # Reusable UI components (Button, Badge, Card…)
│   │   ├── pages/              # Route-level pages (Home, Profile, Leaderboard…)
│   │   ├── game/               # Shadowrift Duel — Canvas 2D game engine
│   │   │   ├── engine.js       # Game loop, collision, relic spawning
│   │   │   ├── renderer.js     # Canvas draw calls
│   │   │   └── input.js        # Keyboard input handler
│   │   ├── hooks/              # useSocket, useAuth, useDuel, useMatchmaking
│   │   └── styles/             # CSS custom properties, global reset
│   └── public/
│       └── assets/
│           ├── fonts/          # Self-hosted display font (Cinzel or equivalent)
│           ├── images/         # Emblem presets, rank badge SVGs
│           └── sounds/         # Ambient audio (off by default)
├── server/
│   ├── routes/                 # REST API route handlers
│   │   ├── auth.js             # POST /register, POST /login, POST /logout
│   │   ├── users.js            # GET /profile, PATCH /profile
│   │   ├── leaderboard.js      # GET /leaderboard
│   │   ├── covenants.js        # CRUD covenant endpoints
│   │   └── tournaments.js      # Tournament bracket endpoints
│   ├── ws/                     # WebSocket server
│   │   ├── matchmaking.js      # Queue management, rank-band pairing
│   │   ├── duelLoop.js         # Game tick, state broadcast, relic spawning
│   │   ├── spectator.js        # Delayed state stream for spectators
│   │   └── chat.js             # Lobby and in-duel chat rooms
│   └── db/                     # Database layer
│       ├── queries.js          # All SQL queries
│       └── helpers.js          # XP calc, rank point calc, tier resolution
├── db/
│   ├── migrations/             # Version-controlled schema (001_init.sql…)
│   ├── seed.js                 # Generates 20 dark-fantasy seed players
│   └── nightreign.db           # SQLite file (auto-created by npm run db:setup)
├── data/
│   └── game-config.json        # ← EDIT THIS to change all game rules
├── README.md                   # This file
├── API_REFERENCE.md            # All REST endpoints + WebSocket message schema
└── METHODOLOGY.md              # XP formula, rank points, seed data policy
```

---

## Editing Game Rules

All tunable parameters live in **`data/game-config.json`**:

```json
{
  "scoreToWin": 10,
  "matchDurationSeconds": 180,
  "relicSpawnIntervalSeconds": 30,
  "rankTiers": [
    { "title": "Shade",      "abbreviation": "SH", "minPoints": 0    },
    { "title": "Wraith",     "abbreviation": "WR", "minPoints": 100  },
    { "title": "Revenant",   "abbreviation": "RV", "minPoints": 300  },
    { "title": "Shadowlord", "abbreviation": "SL", "minPoints": 600  },
    { "title": "Nightreign", "abbreviation": "NR", "minPoints": 1000 }
  ],
  "xpPerWin": 50,
  "xpPerLoss": 15,
  "rankPointsPerWin": 25,
  "rankPointsPerLoss": -10,
  "seedPlayerCount": 20
}
```

Edit any value → save → reload the browser. No rebuild, no code change.

---

## Data Sources

| Data | Source | Label |
|------|--------|-------|
| 20 starter players | Auto-generated by `@faker-js/faker` on `npm run db:setup`. Dark-fantasy usernames (VoidWalker, AshenBlade, CrimsonWraith…). | **[SEED]** badge on leaderboard |
| Match results, XP, rank points | Generated from real matches played in the current session. | No label — this is live data |
| Arena themes | Cycled from a fixed list in `game-config.json` based on total match count. | Config-driven |

**No data is imported from a real gaming database. No stats are invented and displayed as live.**
See `METHODOLOGY.md` for the full XP and rank point calculation formulae.

---

## Rank Tier Reference

| Tier | Title | Points | Glow |
|------|-------|--------|------|
| 1 | Shade | 0 – 99 | Grey |
| 2 | Wraith | 100 – 299 | Soft violet |
| 3 | Revenant | 300 – 599 | Indigo |
| 4 | Shadowlord | 600 – 999 | Bright violet |
| 5 | **Nightreign** | 1000+ | **Gold crown** |

The Nightreign title belongs to the single player with the highest rank points above 1000. It transfers automatically.

---

## Browser Support

| Browser | Status |
|---------|--------|
| Chrome (latest stable) | ✅ Fully tested |
| Firefox (latest stable) | ✅ Fully tested |
| Safari | Not guaranteed |
| Mobile | Out of scope for v1 |

Minimum viewport: **1280 × 720**

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React 18 + Vite |
| Styling | Tailwind CSS + CSS custom properties |
| 3D Lobby | Three.js |
| Charts | Recharts |
| Backend | Node.js + Express |
| Real-Time | Socket.io (WebSockets) |
| Database | SQLite (better-sqlite3) |
| Auth | express-session + bcrypt |
| Seed | @faker-js/faker |
| Display Font | Cinzel (self-hosted) |

---

## Troubleshooting

**Port conflict:**
```bash
npx kill-port 3000 5173
npm run dev
```

**Database needs reset:**
```bash
npm run db:reset   # Wipes and recreates nightreign.db + re-runs seed
```

**Game won't sync between tabs:**
Both tabs must use the same `localhost:5173` origin. Cross-origin WebSocket connections are blocked by default.

**Leaderboard not updating after match:**
Check that the WebSocket server is running (`localhost:3000`). The leaderboard subscribes to a `leaderboard:update` event on the socket connection.

---

*The void is real. Enter the Rift.*
