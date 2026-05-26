# ANSWERS.md

---

## 1. How to run

No install required. The entire app lives in `index.html`.

**Simplest:** double-click `index.html` — it opens directly in any browser.

**Served (recommended):**
```bash
npx serve .        # visit http://localhost:3000
# or
python -m http.server 8080   # visit http://localhost:8080
```

No Node, no npm, no build. Fresh machine just needs a browser.

> Deployed URL: _(add after deploying to Vercel / Netlify / GitHub Pages)_

---

## 2. Stack & design choices

**Stack:** Vanilla HTML/CSS/JS — no framework, no bundler.

For a self-contained timer, a framework would add friction without benefit: no component tree, no reactive data binding needed beyond a handful of DOM updates. One `index.html` means zero install steps, which I considered part of the UX.

**Decision 1 — The ring takes ~65% of the viewport height.**

The timer is the entire point of the screen, so I let it own the vertical space. The SVG ring (`r="115"`, sitting in a 272×272 canvas) is sized so the `mm:ss` digits land at roughly `clamp(58px, 14vw, 76px)` — legible at a glance without leaning in, and still fitting inside the ring on a 360 px phone. I could have made the ring decorative and the digits the primary focus, but the ring-as-progress-bar earns its size by doing real work: you see _where in the session_ you are without reading the number.

**Decision 2 — Accent color shifts between focus (amber `#e8975a`) and break (teal `#5ecfb1`).**

This affects the ring stroke, the mode-label dot, the primary button background, and the settings panel's save button — essentially everything that "belongs" to the current mode. The shift is immediate state communication: you never need to read "FOCUS" or "BREAK" to know what mode you're in. I chose amber and teal specifically because they read as warm/energetic vs cool/restful at a glance, which maps naturally to the modes, and both have enough contrast against the near-black `#0d0c0b` background to pass WCAG AA for large text.

---

## 3. Responsive & accessibility

**Responsive:**

On a 360 px phone the timer fits in a single centered column: the SVG ring is sized with a fixed pixel canvas (272 px) that fits within the screen with 20 px side padding, and the digit size is clamped downward with `clamp(58px, 14vw, 76px)`. There's no horizontal scrolling at 360 px. Below 390 px, an explicit media query drops the ring to 256 px. On a 1440 px laptop the app stays centered in a `max-width: 460px` column — the content doesn't sprawl, which keeps the focus-tool feel intact.

**Accessibility handled:**

- **Keyboard navigation** — `Space` starts/pauses, `R` resets (guarded to ignore when focus is inside a form element). All buttons have `:focus-visible` styles that clearly show focus ring using `outline: 2px solid var(--accent)`.
- **ARIA** — the mode label uses `aria-live="assertive"` so a screen reader announces "FOCUS" / "BREAK" on transition. The primary button's `aria-label` updates dynamically ("Start timer" / "Pause timer" / "Resume timer") so the state is conveyed to non-visual users. The settings dialog uses `role="dialog"`, `aria-modal="true"`, and `aria-labelledby`.
- **Focus trap in modal** — Tab and Shift+Tab cycle only within the settings overlay while it's open; Escape closes it and returns focus to the settings button.
- **`prefers-reduced-motion`** — the ring glow pulse, dot blink, digit beat, and flash overlay animations are all suppressed.

**Accessibility skipped:**

I did not announce each second to screen readers — `aria-live` on the countdown would read "25:00", "24:59", "24:58"… which would be unusable noise. The right solution with more time would be an announcer that speaks only at meaningful intervals (e.g. every 5 minutes, and at "1 minute remaining"), keyed off the value rather than every tick. I knowingly omitted this because a noisy live region is worse than none.

---

## 4. AI usage

I used Claude (Anthropic) to generate the initial structure and then iterated on it.

**What I asked / what it gave me:**

1. _Initial scaffold_ — Asked for a vanilla HTML Pomodoro timer with an SVG ring, Web Audio completion sound, and localStorage history. It gave me a working base with a correct ring circumference calculation (`2πr`) and a single-oscillator beep.

2. _Audio_ — The AI's first pass used a single sine oscillator at 440 Hz — functional but anticlimactic. I changed this to a three-note C–E–G arpeggio (523 / 659 / 784 Hz, staggered 0.16 s apart) with a soft exponential decay envelope. The chord gives the "session done" moment weight; the arpeggio timing means it sounds like a rising acknowledgement rather than an alarm. This was the most deliberate override: the AI treated audio as a checkbox, I treated it as part of the experience.

3. _History storage_ — The AI stored history as a flat array and compared `new Date().toDateString()` on load. I restructured it to store `{ date: "2026-5-26", sessions: [...] }` as a keyed object, so the date comparison is a simple string equality check that can't drift due to timezone or locale differences in `toDateString()`.

4. _Dot-grid background_ — The AI used a CSS SVG data-URL noise texture. I swapped it for a `radial-gradient` dot grid using `background-image: radial-gradient(var(--border) 1px, transparent 1px)` with `background-size: 28px 28px`. The dot grid is lighter on the GPU, more intentional aesthetically, and degrades gracefully when the CSS variable is unsupported.

---

## 5. Honest gap

**The settings panel doesn't validate or constrain live input — it only clamps via the +/− steppers.** If someone manages to get `tmpFocus` into a bad state (e.g. via DevTools or a future direct-input field), the timer would receive a `NaN * 60` time value and silently break. With another day I'd add a `clamp(1, value, 99)` guard in every place `state.focusMins`/`state.breakMins` is written, and add a proper `<input type="number">` inside the stepper so keyboard users can type a value directly (currently they must click +/− repeatedly to reach, say, 45 minutes). I'd also add a brief inline confirmation ("Applied — timer reset") that appears for 2 seconds after saving settings, so it's unambiguous that the change took effect.


---

## Additional Improvements

- Added a productivity-focused To-Do List section where users can manage multiple daily tasks.
- Added task completion notifications to improve engagement and usability.
- Added a Dark / Colorful theme switcher using localStorage persistence.
- Improved visual interactions and modern UI polish for better user experience.
