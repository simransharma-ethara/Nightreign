# Build a Locally Runnable Full-Stack Online Gaming Platform called "Nightreign"

Build a dark, atmospheric, real-time competitive gaming platform named **Nightreign** — a shadow-world battle arena where players duel, rise through cursed rank tiers, forge covenants, and carve their name onto an eternal leaderboard. The platform runs entirely on a local machine with a single command. No paid service, no API key, no cloud account required.

> *"In the Nightreign, there are no kings. Only survivors."*

---

## Required Experience

### Homepage
- Full-bleed void dark hero section with an **animated void particle field** (Canvas or CSS).
- A **rank ticker** showing the current top 5 players with their rank tier badge and rank points — updated live from real match data.
- An **active duel counter** showing how many matches are in progress right now.
- A pulsing **"Enter the Rift"** call-to-action button that routes to the matchmaking lobby.
- A navigation bar with links to: Leaderboard, Profile, Covenants, Tournaments.

### Authentication
- User **registration** (username, email, password) and **login** with session persistence (page reload keeps the user logged in).
- **Logout** clears the session.
- Form validation with Nightreign-themed error messages (e.g. *"The Rift cannot verify that name. Try again."*).
- No third-party OAuth required.

### Player Profile
- Avatar upload (stored locally).
- Username and current **rank tier badge** with glow effect matching the tier colour.
- **Shadow XP progress bar** (animated ring or bar showing progress to the next tier).
- Win / loss / draw counts and **win rate percentage**.
- **Paginated match history table**: opponent name, result, XP earned, rank delta, date.
- Link to the player's full **Stat Dashboard**.

### Matchmaking
- A **queue lobby** that auto-pairs players within the same rank band.
- Displays: queue position, estimated wait time, and opponent's rank tier once matched.
- **Leave Queue** button cancels the search.
- A **Challenge** button to directly invite a specific player by username.

### Shadowrift Duel — The Core Game
- A **real-time 1v1 browser game** using WebSockets. No download. Playable directly on the platform page.
- **Controls**: WASD or arrow keys to move, Space to strike, Shift to dodge.
- **Win condition**: deplete the opponent's HP to zero, or outscore them when the 3-minute timer expires.
- **Power relics** spawn on the arena map every 30 seconds (speed boost, shield, strike amplifier).
- Arena theme changes based on total platform match count (Void Hollow → Ashen Keep → Crimson Fen, cycling).
- A **match HUD overlay**: both players' HP bars, score, countdown timer, minimap, and in-duel chat sidebar.

### Post-Match Results Screen
- Animated entry (slide-up or fade-in — no instant appear).
- Shows: winner / loser announcement, Shadow XP earned, rank tier change (animated tier badge transition), top stat (most strikes, fastest kill), match duration.
- **Rematch** and **Return to Rift** buttons.

### Global Leaderboard
- Top 100 players sorted by rank points (default).
- **Column-header sorting**: rank points, win rate, total duels.
- **Username search** with live filter.
- **Pagination**: 25 per page, forward/back controls.
- **Live update**: leaderboard refreshes automatically when a match ends (no full page reload).
- Seed data players display a **[SEED]** badge. The Nightreign title holder has a unique gold crown indicator.

### Covenant System (Clans)
- **Create a Covenant**: name, 4-character tag, emblem (image upload or preset icon).
- **Join a Covenant** via invite link or search.
- **Covenant page**: emblem, name, tag, covenant rank, member roster with individual rank badges, and a **rolling activity feed** of recent member duels (e.g. *"AshenBlade defeated VoidWalker in Crimson Fen"*).

### Live Chat
- **Rift Chat** (global lobby): all logged-in players can post. WebSocket-powered — messages appear without page reload.
- **Duel Chat** (in-match sidebar): visible to both players and spectators during a duel.
- **Profanity filter toggle** in user settings (on by default).

### Stat Dashboard
- Accessible from the player profile page.
- At least **two interactive charts** built from real gameplay data (not seed):
  - Win rate over time (line chart, last 30 days).
  - Duel activity heatmap by hour of day (which hours the player is most active).
  - Optionally: average HP remaining on wins (bar chart by arena theme).
- Charts use the Nightreign colour scheme (violet lines on dark background).

### Spectator Mode
- Any logged-in player can click **"Watch"** on an active duel from the Leaderboard or a live match list.
- Spectator view updates with a **≤1.5 second delay**.
- Shows: live HP bars, score, countdown timer, read-only chat.
- **Spectator count** is visible to the duelling players in their HUD.

### Rift Tournament
- Admin creates a bracket (8 or 16 players).
- Players register from the tournament page.
- **Bracket viewer** renders all matchups and updates live as results come in.
- Winner auto-advances. Final winner receives a tournament trophy on their profile.

### Methodology / Data Note Panel
- Accessible from the leaderboard and player profile pages.
- Must clearly state:
  - How Shadow XP is awarded per match (formula).
  - How rank points are calculated (win/loss delta).
  - What data is **seeded** (generated at setup, flagged with `is_seed = true`) versus **live** (generated from real played matches).
  - When the seed data was last regenerated.
- No invented values may be displayed as if they are the result of real played matches in the current session.

---

## Navigation & Controls

| Control | Action |
|---------|--------|
| W / ↑ | Move up |
| S / ↓ | Move down |
| A / ← | Move left |
| D / → | Move right |
| Space | Primary strike |
| Shift | Dodge / dash |
| Esc | Pause / quit duel |

- All interactive elements must be keyboard-navigable (Tab key order is logical).
- A **keybinding settings page** allows players to remap all game controls.
- ARIA labels on all icon-only buttons.

---

## Configuration File

All tunable game parameters must live in a single file: **`data/game-config.json`**.

```json
{
  "scoreToWin": 10,
  "matchDurationSeconds": 180,
  "relicSpawnIntervalSeconds": 30,
  "rankTiers": [
    { "title": "Shade",       "abbreviation": "SH", "minPoints": 0    },
    { "title": "Wraith",      "abbreviation": "WR", "minPoints": 100  },
    { "title": "Revenant",    "abbreviation": "RV", "minPoints": 300  },
    { "title": "Shadowlord",  "abbreviation": "SL", "minPoints": 600  },
    { "title": "Nightreign",  "abbreviation": "NR", "minPoints": 1000 }
  ],
  "xpPerWin": 50,
  "xpPerLoss": 15,
  "rankPointsPerWin": 25,
  "rankPointsPerLoss": -10,
  "seedPlayerCount": 20
}
```

Changing any value and reloading must update game behaviour and UI with **no code change and no rebuild**.

---

## Visual Design Requirements

Follow the **Nightreign Design System** exactly:

| Token | Hex | Usage |
|-------|-----|-------|
| `--nr-void` | `#0D0618` | Page background |
| `--nr-deep` | `#1A0A2E` | Card / panel background |
| `--nr-shadow` | `#2D1052` | Elevated surfaces, nav |
| `--nr-violet` | `#7B2FBE` | Primary brand, buttons, borders |
| `--nr-glow` | `#C084FC` | Highlights, XP bar, active states |
| `--nr-crimson` | `#BE2F2F` | Danger, low HP, enemy highlight |
| `--nr-silver` | `#C8D0DC` | Primary text |
| `--nr-muted` | `#6B7280` | Secondary text, captions |
| `--nr-gold` | `#F59E0B` | Trophy, rank badge, tournament |

- Display headings: a **dark fantasy serif or gothic display font** (self-hosted in `/assets/fonts/`). Cinzel or equivalent.
- Body / UI: clean sans-serif (Inter or system-ui). Never a novelty font for body copy.
- All themed copy uses Nightreign universe language (see PRD Section 10).

---

## Animated Transitions

- The matchmaking flow must include a **smooth animated transition**: lobby → void countdown animation → duel start (no jarring cut).
- The post-match screen must **animate in** (slide-up or fade — never instant).
- The landing page hero must have at least one **continuous ambient animation** running (particle field, aurora pulse, or animated void silhouette).
- Rank tier upgrades must trigger a **badge transition animation** on the post-match screen.

---

## Delivery Shape

```
Nightreign_Delivery/
├── package.json              ← npm run dev starts everything
├── client/                   ← React 18 + Vite frontend
│   ├── src/
│   │   ├── components/       ← Reusable UI components
│   │   ├── pages/            ← Route-level pages
│   │   ├── game/             ← Shadowrift Duel canvas logic
│   │   ├── hooks/            ← useSocket, useAuth, useDuel
│   │   └── styles/           ← CSS variables, global styles
│   └── public/
│       └── assets/           ← Self-hosted fonts, images, sounds
├── server/
│   ├── routes/               ← REST API handlers
│   ├── ws/                   ← WebSocket server (matchmaking + duel loop)
│   └── db/                   ← Database queries and helpers
├── db/
│   ├── migrations/           ← Version-controlled schema migrations
│   └── nightreign.db         ← SQLite file (auto-created on setup)
├── data/
│   └── game-config.json      ← THE editable config (single source of truth)
├── README.md
├── API_REFERENCE.md
└── METHODOLOGY.md
```

---

## Technology Constraints

- Stack is the developer's choice. **React 18 + Node.js + Socket.io** is the recommended default.
- Must run in the latest stable **Chrome and Firefox** on desktop.
- Must be usable at **1280×720 and up**. No broken layouts, no clipped labels.
- Game frame rate must stay **smooth on a mid-range laptop** (target 30+ FPS).
- **All assets inside the repository**. No external CDN that could go dark.
- **No paid service, no API key, no signup** required for the evaluator.

---

## What This Should Feel Like

The evaluator should be able to:

1. Run **one command** and have the full platform load in the browser in under 2 minutes.
2. **Register**, queue for a duel, **play a match** against another tab, and see their profile, rank, and leaderboard position update in real time.
3. Feel the **Nightreign universe** on every screen — from rank badge glows to void-themed error messages.
4. **Browse a live leaderboard**, join a covenant, and watch a duel as a spectator without touching a single line of code.

This is a **complete, living gaming platform** — not a mockup, not a video, not a static prototype. The void is real. Enter the Rift.
