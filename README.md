# خاتم — Khatim: Signal Routing Puzzle

A logic-gate signal-routing puzzle game with an original mechanic, built as a single-page Progressive Web App. Visual language is drawn from Islamic geometric pattern design — zellij star-tiling (khatim), girih lattice backgrounds, and eight-pointed star nodes — applied to a circuit-simulation puzzle genre that doesn't otherwise exist in this exact form.

**[▶ Play it live](#)** — *(replace with your deployed URL once published — see Deployment below)*

![Khatim icon](icon-512.png)

---

## What it is

You are given a grid containing one or more **Sources** (emit a 0 or 1 signal), a set of **Targets** (each wants a specific 0/1 value to "light up"), and open **wire** cells connecting them. You are handed a limited inventory of **logic gates** — AND, OR, NOT, XOR, SPLIT, DELAY — and must drag them onto the wire path so that, once the signal is run, every Target receives the value it wants.

It plays like a cross between a wiring puzzle and a logic-gate teaching tool, scored on solve time and gate efficiency (S / A / B / F ranking), across 6 hand-built levels of increasing complexity.

### Core mechanics
- **Drag-and-drop gate placement** from a side library onto the grid (mouse + touch supported)
- **Right-click / long-press to remove** a placed gate and return it to inventory
- **BFS signal propagation engine** — written from scratch, no physics or graph library — that walks the wire graph from every Source, evaluates gates as it reaches them (collecting all incoming edges as gate inputs), and marks Targets lit/failed based on the final signal
- **Live HUD**: score, countdown timer with color-shifting urgency bar, gates-remaining counter
- **Win/fail overlay** with per-level efficiency grading

### Design system
| Token | Value | Use |
|---|---|---|
| `--bg` | `#0B0F1A` | night-indigo background |
| `--panel` | `#10192B` | side panel / HUD |
| `--gold` | `#D4AF6A` | primary accent, sources of "current" |
| `--teal` | `#2E8C86` | success state, lit targets |
| `--carmine` | `#B5494A` | fail state |
| `--azure` | `#4A7FB5` | source nodes / OR gate |

Typography: **Amiri** (calligraphic serif, titles), **Reem Kufi** (geometric Kufic-style, UI labels/numerals), **Tajawal** (body text) — all loaded from Google Fonts.

Signature visual motifs: an animated rotating eight-pointed star on the title screen; khatim-star outlines for every Source/Target node (drawn procedurally on `<canvas>`, not as image assets); gates rendered as rotated diamond tiles; a faint cross-hatched girih pattern as the page background.

---

## Tech stack

Deliberately zero-dependency:

- **HTML5** — single-document structure
- **CSS3** — custom properties, `clip-path` for beveled-corner buttons, `backdrop-filter`, keyframe animation for the rotating star mark
- **Vanilla JavaScript (ES6+)** — no frameworks, no build step
- **HTML5 Canvas API** — entire grid, gate icons, wire glow, and khatim-star nodes are drawn procedurally every frame; nothing is a static image except the two app icons
- **Service Worker + Web App Manifest** — installable, offline-capable PWA

Everything needed to run this game is in this repository. There is no `npm install`, no bundler, no backend.

---

## Repository structure

```
khatim-signal-puzzle/
├── index.html              All game markup, styles, and logic (single-file app)
├── manifest.webmanifest    PWA manifest — app name, icons, theme color, display mode
├── sw.js                   Service worker — caches assets for offline play
├── icon-192.png            App icon, 192×192 (home-screen / Android)
├── icon-512.png            App icon, 512×512 (splash screen / iOS)
├── LICENSE
└── README.md
```

`index.html` intentionally contains the CSS and JS inline rather than split into separate files. For a project this size, that keeps the entire game inspectable in one read and trivially portable — copy the one file and it runs.

---

## Running it locally

No build tools, no install step. Two options:

**Option A — just open it**
Double-click `index.html`. It runs directly in any modern browser. (The service worker won't register over `file://`, which is fine — the game works identically with or without it; you just won't get offline caching.)

**Option B — serve it locally** (recommended, lets you test the PWA install flow)
```bash
git clone https://github.com/<your-username>/khatim-signal-puzzle.git
cd khatim-signal-puzzle
python3 -m http.server 8080
# then open http://localhost:8080
```

---

## Deployment

This is a fully static site — any static host works. Two free options:

### GitHub Pages
1. Push this repo to GitHub
2. Repo → **Settings → Pages**
3. Source: **Deploy from a branch** → branch `main`, folder `/ (root)`
4. Your live URL will be `https://<your-username>.github.io/khatim-signal-puzzle/`

### Netlify
1. [netlify.com](https://netlify.com) → sign up free
2. Drag this folder onto the deploy drop zone (or connect the GitHub repo for auto-deploys on push)
3. Get an instant `*.netlify.app` URL, renameable in site settings

Once live, open the URL on a phone and use the browser's **"Add to Home Screen"** option — it installs as a standalone app icon with no browser chrome, using `icon-192.png` / `icon-512.png` as the app icon.

---

## How the puzzle logic works (for anyone reading the code)

Each level is defined as a small ASCII grid (`S` = source, `T` = target, `W` = wire, `G` = open gate slot, `.` = empty, `#` = wall) plus metadata: per-source signal values, per-target desired values, available gate types/counts, and a time limit. See `makeLevels()` in `index.html`.

At solve time, `runSignal()`:
1. Resets all cell signals
2. Seeds each Source cell with its defined 0/1 value
3. Runs a breadth-first traversal outward from all Sources simultaneously, visiting each wire/gate/target cell once
4. When traversal reaches a cell with a player-placed gate, it gathers the signal values of that cell's already-resolved neighbors as gate inputs and evaluates the gate (`applyGate()`) to produce the outgoing signal
5. Once traversal completes, every Target cell's final signal is compared against its desired value to determine lit/failed state

This means gate order along a path matters, and a single SPLIT gate can feed two independent downstream paths with the same source signal — which is what levels 4–6 are built around.

---

## License

MIT — see [LICENSE](LICENSE). Free to fork, remix, or build on.

---

## Author

Built solo by **Salman Ferdous Rafi** — self-taught developer, Dhaka, Bangladesh. Designed and coded entirely from a mobile phone, no formal training, no team.

If you're a recruiter or engineer looking at this repo: this project was chosen specifically to demonstrate an original game mechanic (not a clone of an existing puzzle format), a from-scratch simulation/traversal engine, and a coherent custom visual design system — all without a single external JS library.
