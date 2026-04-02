# CricScore — Product Requirements Document

**Version:** 1.0  
**App Version:** v6 (March 2026)  
**Document Date:** April 2, 2026  
**Product Type:** Progressive Web App (PWA) — Single-File SPA

---

## 1. Product Overview

### 1.1 Product Summary

CricScore is a personal cricket management PWA that enables cricket teams and organizers to manage teams, players, fixtures, live scoring, tournaments, and series from a single application. It works fully offline and syncs to the cloud when connected.

**Tagline:** *Your personal cricket manager — teams, fixtures, live scoring and stats*

### 1.2 Problem Statement

Amateur and club-level cricket teams lack affordable, mobile-friendly tools to manage:
- Team rosters and player profiles
- Fixture scheduling and match results
- Ball-by-ball live scoring during matches
- Multi-format tournament and series organization
- Team communication and match stories

### 1.3 Target Users

| User Type | Description |
|-----------|-------------|
| Team Captain / Manager | Organizes squads, schedules fixtures, runs live scoring |
| Club Admin | Manages multiple teams under a single club |
| Cricket Player | Views their career stats, availability, and team info |
| Tournament Organizer | Sets up knockout/league formats with brackets and points tables |

### 1.4 Platform

- **Delivery:** Progressive Web App (PWA) — single HTML file, installable on iOS/Android/Desktop
- **Backend:** Supabase (PostgreSQL + Auth + Storage)
- **Offline:** IndexedDB + localStorage offline-first cache
- **Responsive:** Mobile-first with tablet and desktop breakpoints

---

## 2. Goals & Success Metrics

### 2.1 Product Goals

1. Allow any cricket team to be up and running with live scoring in under 5 minutes
2. Provide a frictionless offline-first experience — app must work without internet
3. Support all popular cricket formats (T5–T50, ODI, Test, tape-ball)
4. Enable tournament organizers to run full knockout or round-robin events
5. Give players visibility into their career stats

### 2.2 Key Metrics

| Metric | Target |
|--------|--------|
| Time-to-first-match-scored | < 5 minutes from sign-up |
| Offline save success rate | 100% with auto-sync on reconnect |
| Mobile usability (< 600px) | Full feature parity |
| Player data accuracy | Ball-by-ball stats reconcile to match total |

---

## 3. Features & Requirements

### 3.1 Authentication

**Description:** Email/password authentication with offline fallback.

**Requirements:**
- Sign up with name, email, and password
- Sign in with email and password
- Persist session across refreshes via localStorage/sessionStorage/cookie fallback chain
- Support offline login from cached session
- Custom Supabase project URL + API key configuration (self-hosted option)
- Share-link access (read-only scorecard view without login)

---

### 3.2 Dashboard

**Description:** Home screen with summary stats and quick-access to key actions.

**Requirements:**
- Display 4 quick-stat tiles: Total Teams, Total Players, Total Games, Completed Games
- Show win/loss ratio for primary team
- Display upcoming fixtures (next 3)
- Interactive onboarding checklist (4 steps):
  1. Create first team
  2. Add players to team
  3. Schedule a game
  4. Record a live score
- Progress bar for onboarding completion
- Recent games feed with status badges
- Cloud sync loading indicator

---

### 3.3 Team Management

**Description:** Create and manage cricket teams with squads, colors, and metadata.

**Requirements:**

#### Team Creation
- Name, team type (Men's / Women's / Youth / Mixed / Indoor / Tape-ball)
- Format preset selector (T5, T10, T20, T25, ODI, Test, Custom)
- Custom overs input for non-standard formats
- Home ground field
- Team color picker (12-color palette, gradient avatar)
- Notes/description field

#### Team Card Display
- Avatar badge with initials in team color
- Stats: Played / Won / Lost
- Player avatar bubbles (first 5, "+N more" overflow)
- Actions: View Squad, Add Game, Invite

#### Squad Management
- Add players from global registry to team
- Drag-and-drop reorder (batting order)
- Inline player editing within squad context
- Captain (👑) and Vice-Captain (🌟) designation
- Floater players (⚡) can belong to multiple teams
- Remove player from squad (without deleting from registry)

#### Team Info & Edit
- Edit all team metadata
- Delete team with confirmation dialog

#### Invite System
- Generate shareable invite codes
- QR code display for in-person sharing
- Set optional expiry date
- Join team via invite code

---

### 3.4 Player Registry

**Description:** Global player database independent of teams.

**Requirements:**
- Player list with search/filter
- Player card: name, jersey number, role badge, bowling style, floater indicator
- **Add Player fields:** Name, jersey #, role (Batsman / Bowler / All-rounder / Wicket-keeper), batting style (Right/Left-hand), bowling style (Fast/Medium/Off-spin/Leg-spin/Left-arm spin), floater toggle
- Avatar upload (image file)
- Career Stats modal:
  - Aggregate batting: matches, runs, average, strike rate, highest score, 50s, 100s
  - Aggregate bowling: matches, wickets, average, economy, best figures
- Edit and delete player

---

### 3.5 Games / Fixtures

**Description:** Schedule, track, and manage match fixtures.

**Requirements:**

#### Game Card Display
- Team matchup layout (Home vs Away)
- Score display for completed matches (e.g., "187/4 (20)")
- Date, time, venue, format, overs
- Status badge: Upcoming (blue) / Live (red + pulse) / Completed (green) / Cancelled (gray)
- Color-coded left border per status

#### Add / Edit Game
- Your team (dropdown) + opponent (dropdown or custom text)
- Date and time inputs
- Format preset selector + custom overs
- Venue (from registry or free-text)
- Tournament / Cup association (optional)
- Status selector
- Score inputs for completed matches ("runs/wickets")
- Result description (free text, e.g., "Won by 5 wickets")
- Match fee: Free or Paid (amount + currency)

#### Solo Batting Mode
Three modes for handling last-batter situations:
- **Off:** Always require 2 batters on crease
- **Auto:** Solo mode only when last recognized batter is in
- **Always:** Solo mode from first ball

---

### 3.6 Live Scoring

**Description:** Ball-by-ball live scoring engine — the core feature of the app.

**Requirements:**

#### Match Setup
- Toss system: coin flip animation, call Heads/Tails, winner chooses Bat/Bowl
- Select batting team, choose innings (1st/2nd)
- Carry over setup from game fixture

#### Live Scoreboard (persistent during scoring)
- Large score display: `45/2 (8.3)`
- Current Run Rate and Required Run Rate (for 2nd innings)
- Active batters: striker and non-striker with runs, balls, dot count
- Current partnership: runs, balls, 4s, 6s

#### Ball Input Panel
- Quick-tap buttons (44px minimum on mobile):
  - Runs: **0, 1, 2, 3, 4, 6**
  - **W** (Wicket), **Wd** (Wide), **Nb** (No-ball), **Bye**
- Keyboard shortcuts (desktop): `0-6`, `W`, `E` (wide), `N` (no-ball), `B` (bye)
- Bowler change modal (end of each over)
- Batter change modal (after wicket or over)

#### Extras Tracking
- Wides, no-balls, byes, leg-byes tracked separately
- Extras shown in innings summary

#### Innings Management
- Target calculation for 2nd innings
- Mark innings as completed
- Support for 2-innings-per-side matches (longer formats)

#### Stats Panel (in-match)
- Batting scorecard table: Name, R, B, 4s, 6s, SR, Dismissal
- Bowling figures table: Name, O, M, R, W, Econ
- "Batting now" row highlighted

#### Post-Match
- Man of the Match selector (top performers auto-suggested)
- Match fee collection status
- Save final scorecard to game record
- Generate shareable read-only scorecard link + QR code

#### Offline & Device Support
- Wake Lock API to prevent screen timeout during scoring
- Vibration API for haptic feedback on run input
- Auto-resume live scoring if app is backgrounded or refreshed

---

### 3.7 Tournaments

**Description:** Organize multi-team knockout or round-robin tournaments.

**Requirements:**

#### Tournament Creation (2-step modal)
- **Step 1:** Name, format (Knockout / Round-Robin), teams count, groups configuration
- **Step 2:** Match schedule generation — group stage fixtures + knockout brackets

#### Tournament View
- Progress bar (% of matches completed)
- Matches grouped by date and stage
- Color-coded stages: Group (blue) / Quarter-Final (orange) / Semi-Final (blue) / Final (green)
- TBD placeholder matches for knock-out slots (auto-populated on progression)
- Individual match result entry inline

#### Points Table (Round-Robin)
- Columns: Rank, Team, P, W, L, Pts, NRR
- Visual separator line between qualified and eliminated teams
- Qualification badge highlight

#### Knockout Bracket
- Visual bracket layout (QF → SF → Final)
- Team names in bracket slots, winner highlighted
- Scores shown per match

---

### 3.8 Series

**Description:** Schedule and track a bilateral series between 2–3 teams.

**Requirements:**
- Series creation (2 steps): name, format, match count (2/3/5/7), team selection, match scheduling
- Series card: name, status, teams, match played count, wins per team
- Series detail modal: full match schedule, head-to-head stats, points table
- Filter: All / Active / Completed

---

### 3.9 Head-to-Head

**Description:** Compare win/loss records between any two teams.

**Requirements:**
- Select Team A and Team B from dropdowns
- Display: total matches, wins per team, last 5 encounters
- Each encounter row: date, venue, result, match type

---

### 3.10 Venues

**Description:** Registry of cricket grounds for fixture association.

**Requirements:**
- Add venue: name, location/address
- List venues with associated fixture count
- Auto-complete in game creation and editing
- Delete venue (with validation against existing fixtures)

---

### 3.11 Feed / Stories

**Description:** Social-style activity feed for the team community.

**Requirements:**

#### Post Composer
- Text area with character counter
- Post type selector: Story / Highlight / Milestone / Match Result / Photo
- Optional team tag

#### Post Card
- Author avatar and name
- Relative timestamp (e.g., "3m ago", "2h ago", "Yesterday")
- Post type badge (color-coded)
- Post body text
- Team mention tag (if tagged)

#### Interactions
- Like / Unlike with ❤️ button and count
- Delete own posts (with confirmation)
- Feed sorted by recency (newest first)

---

### 3.12 User Profile & Settings

**Requirements:**

#### My Profile
- Display name
- Avatar (upload from device, stored in Supabase Storage)
- Jersey number
- Years playing
- Home city / ground
- Playing role
- Batting hand (Right/Left)
- Bowling style (Pace or Spin, detailed options)

#### Settings
- Profile link
- Club Mode toggle
- App version display

#### Club Mode
- Group multiple teams under a single club umbrella
- Shared player registry across club teams
- Centralized availability and admin dashboard

---

### 3.13 Availability

**Description:** Mark player availability for upcoming fixtures.

**Requirements:**
- Per-game availability checklist
- Team-based view with player list and available/unavailable toggle
- Availability visible to team captain when selecting playing XI

---

## 4. Data Model

| Table | Key Fields |
|-------|-----------|
| `cs_users` | id, email, name, jersey, role, bat_style, bowl_style, city, avatar_url |
| `cs_teams` | id, owner_id, name, color, home_ground, format, overs |
| `cs_players` | id, owner_id, name, jersey, role, bat_style, bowl_style, is_floater, avatar_url |
| `cs_games` | id, owner_id, team1_id, team2_id, venue, date, format, overs, status, toss, result, score1, score2, fee_type, fee_amount, tournament_id, series_id |
| `cs_innings` | id, game_id, innings_num, batting_team, runs, wickets, balls, extras, target, completed |
| `cs_batting_stats` | innings_id, player_name, runs, balls, fours, sixes, how_out, bowler_name, bat_order |
| `cs_bowling_stats` | innings_id, player_name, overs, maidens, runs, wickets, wides, no_balls, economy |
| `cs_team_players` | team_id, player_id, captain, sort_order |
| `cs_tournaments` | id, owner_id, name, format, status, settings |
| `cs_series` | id, owner_id, name, format, match_count, status, teams |

**Row-Level Security:** All tables scoped to `owner_id` matching authenticated user.

---

## 5. Offline & Sync Architecture

### 5.1 Storage Layers

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Immediate cache | localStorage | All app state cached instantly |
| Offline queue | IndexedDB | Queued writes when offline |
| Session backup | sessionStorage + cookies | Auth session fallback |
| Source of truth | Supabase PostgreSQL | Cloud sync target |

### 5.2 Sync Behavior
- All state written to localStorage immediately on change
- Async write to Supabase in background
- If offline: writes queued in IndexedDB
- On reconnect: queue drained and synced; toast notification shown
- DB data wins on conflict (server is source of truth)
- Tombstone tracking prevents deleted items from being restored via stale local cache

### 5.3 Migration
- One-time migration from legacy "blob" format (v1) to relational tables (v2)
- Migration triggered on first login post-upgrade
- `_v2migrated` flag prevents repeat migration

---

## 6. UX & Design System

### 6.1 Design Principles
- **Sports editorial aesthetic:** Clean white, forest green, slate typography
- **Information density:** Compact cards with max data per viewport
- **Touch-first:** 44px minimum tap targets on mobile
- **Feedback:** Toast notifications, haptic feedback, animation on interactions

### 6.2 Typography
| Role | Font | Weight |
|------|------|--------|
| Display / Headings | Barlow Condensed | 600–900 |
| UI / Body | DM Sans | 300–700 |
| Numbers / Code | JetBrains Mono | 400–700 |

### 6.3 Color Palette
| Token | Use |
|-------|-----|
| `--brand` `#0070f3` | Primary actions, active states |
| `--green` `#059669` | Live status, success states |
| `--red` `#dc2626` | Wickets, destructive actions, live pulse |
| `--amber` `#d97706` | Warnings, toss badge, tournament labels |
| `--violet` `#7c3aed` | Story posts, specialty accents |
| `--teal` `#0891b2` | Highlight posts, secondary accents |

### 6.4 Responsive Breakpoints

| Breakpoint | Layout |
|-----------|--------|
| < 600px (Mobile) | Bottom navigation bar, full-screen bottom-sheet modals, single-column, compact scorecards |
| 600–1024px (Tablet) | Top scrollable nav, centered modals, 2-column grid |
| > 1024px (Desktop) | Sticky header + nav, multi-column grids, hover effects, keyboard shortcuts |

### 6.5 Navigation
- **Desktop/Tablet:** Horizontal scrollable top nav with active underline indicator
- **Mobile:** Fixed bottom tab bar (5 primary tabs) with icon + label + active pill

---

## 7. PWA Requirements

| Requirement | Implementation |
|------------|---------------|
| Installable | Web App Manifest (inline JS blob, no separate file) |
| Home screen icon | Inline SVG cricket bat emoji on dark background |
| App title | "CricScore" |
| Status bar | Black-translucent on iOS, theme-color `#0070f3` on Android |
| Safe area | `env(safe-area-inset-*)` padding for notch/Dynamic Island |
| Offline | localStorage + IndexedDB offline queue |
| Wake Lock | Prevents screen timeout during live scoring |

---

## 8. Supported Cricket Formats

| Format | Overs |
|--------|-------|
| T5 | 5 |
| T6 | 6 |
| T8 | 8 |
| T10 | 10 |
| T15 | 15 |
| T20 | 20 |
| T25 | 25 |
| ODI | 50 |
| Test | Unlimited |
| Custom | User-defined |

---

## 9. Integrations

| Service | Purpose |
|---------|---------|
| Supabase Auth | Email/password sign-up and sign-in |
| Supabase Database | PostgreSQL — all relational data |
| Supabase Storage | Avatar image hosting (public URLs) |
| Google Fonts | Barlow Condensed, DM Sans, JetBrains Mono |
| Web Vibration API | Haptic feedback on ball input (mobile) |
| Screen Wake Lock API | Keep screen on during live scoring |
| Web Crypto API | Secure token generation for invite codes / share links |

---

## 10. Security

- **Row-Level Security (RLS):** All Supabase queries scoped to authenticated `user_id`
- **Session management:** Multi-layer fallback prevents session loss; no plaintext password storage
- **Share links:** Read-only, token-based, no auth required (view-only scope)
- **Invite codes:** Time-limited, cryptographically random tokens
- **Avatar storage:** Public bucket with user-namespaced paths

---

## 11. Out of Scope (Current Version)

The following are explicitly not in scope for v6:

- Real-time multiplayer scoring (second scorer on same match)
- Push notifications for upcoming fixtures
- Video/photo uploads for match media
- Umpire or third-party scoring roles
- Payment gateway integration for match fees
- Public team/league discovery (all data is private to owner)
- Native iOS/Android apps

---

## 12. Appendix — Navigation Structure

```
CricScore
├── Dashboard          — Overview, quick stats, onboarding
├── Teams              — Team cards, squad management, invites
├── Players            — Global player registry, career stats
├── Games              — Fixtures list, add/edit, live scoring
├── Tournaments        — Knockout/RRB tournament management
├── Series             — Bilateral series tracking
├── H2H                — Head-to-head records between teams
├── Venues             — Ground registry
└── Feed               — Team activity feed / stories

Modals (global)
├── Live Scoring Modal — Full ball-by-ball scoring engine
├── Profile Modal      — User profile editing
├── Settings Modal     — App settings, club mode
├── Invite Modal       — Generate/share invite code
├── Share Modal        — Public scorecard link + QR code
├── Availability Modal — Per-game player availability
├── Confirm Modal      — Destructive action confirmation
└── Man of the Match   — Post-match award selection
```
