# Pomodoro Timer

A focused, single-screen Pomodoro timer with daily session history.

## Running locally

### Option 1 — No install, just open
Double-click `index.html` in your file manager, or drag it into any browser.  
Everything is self-contained in one file. No build step, no server required.

### Option 2 — Served (recommended, avoids any browser file:// quirks)

```bash
# Node (npx, no global install needed)
npx serve .

# Python 3
python -m http.server 8080

# PHP
php -S localhost:8080
```

Then visit `http://localhost:3000` (serve) or `http://localhost:8080` (Python/PHP).

## Deployed URL

> Add your Vercel / Netlify / GitHub Pages URL here after deploying.

## Features

- **Configurable durations** — focus and break minutes adjustable via the ⚙ settings panel
- **Start / Pause / Resume / Reset** controls (also: `Space` to toggle, `R` to reset)
- **Live countdown** ticking every second in `mm:ss`
- **Audible cue** on cycle end — C–E–G arpeggio via Web Audio API (no external files)
- **Auto-transition** focus → break → focus with the ring color shifting accordingly
- **Daily history** — completed focus sessions listed below the timer, persisted in `localStorage`, cleared automatically on a new calendar day
- **Responsive** — works from 360 px phone to 1440 px laptop
- **Accessible** — keyboard nav, ARIA labels, focus trapping in modal, `prefers-reduced-motion` respected

## Stack

Vanilla HTML / CSS / JS — no framework, no bundler, no dependencies.  
One file. Open and run.


## New Enhancements Added

- 🌗 Dark Mode / Colorful Mode toggle button
- ✅ Interactive To-Do List
- 🔔 Task completion notification alerts
- 💾 To-do list + theme saved in localStorage
- ✨ Enhanced modern UI interactions
