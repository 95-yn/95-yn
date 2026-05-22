# GitHub Profile README — Cyberpunk Terminal Redesign

**Date:** 2026-05-22
**Repo:** `95-yn/95-yn` (profile README repo)
**Status:** Design approved, ready for implementation plan

## Goal

Push the existing minimal mono-cyan README into a more visually striking "cyberpunk terminal" direction, without bloating the section count. The visual impact should come from the **craft of a custom SVG hero**, not from adding more modules. Final result should fit roughly within one viewport on desktop.

## Constraints & Decisions Locked In

- **Color identity:** mono-cyan `#00f0ff` stays primary. No new accent colors.
- **Direction:** Cyberpunk Terminal — `motd` (message-of-the-day) shell login aesthetic.
- **Implementation:** Custom SVG assets in the repo for the hero and dividers; third-party services for stats and snake (no new GitHub Actions beyond what already exists).
- **Scope:** 3 visual blocks + 1 footer. No `whoami`, `now`, `stack`, `projects`, `activity`, or `metrics` blocks — info that would have gone in those is collapsed into the hero text.
- **No new automation:** existing snake workflow stays; no commit-list updater, no wakatime, no now-playing.

## Layout (top to bottom)

```
┌──────────────────────────────────────┐
│  1. HERO  (custom SVG)               │   ← motd-style terminal welcome
├──────────────────────────────────────┤
│      ── divider (animated SVG) ──    │
├──────────────────────────────────────┤
│  2. STATS  (github-readme-stats)     │   ← 2 cards side-by-side
├──────────────────────────────────────┤
│      ── divider (animated SVG) ──    │
├──────────────────────────────────────┤
│  3. SNAKE  (existing workflow)       │
├──────────────────────────────────────┤
│      footer: EOF + tuttikit link     │
└──────────────────────────────────────┘
```

## Components

### 1. Hero — `assets/hero.svg`

A custom SVG, ~760×280px, embedded in README via `<img>` tag and centered with a `<div align="center">` wrapper.

**Visual contents:**
- Box-drawing border (`┌─ ssh 95-yn@github.com ──┐`-style) in `#6e7681`
- Inside the box, three lines of content:
  1. `$ Last login: today · session #00f0ff` — in cyan prompt + muted grey
  2. Large brand line: `95-yn` (24–28px, cyan with glow filter) followed by `// agents builder` (muted grey comment)
  3. Two lines of tagline:
     - `> building tuttikit — conductor + sub-agents`
     - `> agents that don't break when specs drift_` (trailing cursor)
- A blinking block cursor at end of last line (`<animate>` toggling opacity)
- Faint scanline overlay across the whole box (repeating horizontal lines, ~2% opacity cyan)
- Soft outer glow on the border (`feGaussianBlur` filter)

**Font:** JetBrains Mono (web-safe fallback chain inside SVG: `JetBrains Mono, SF Mono, Menlo, monospace`).

**Why SVG, not markdown:** GitHub renders markdown code blocks inside `<pre>`, which strips the glow / scanline / cursor-animation possibilities. The SVG renders identically on all themes and devices.

**Responsiveness:** SVG uses `viewBox` so it scales down on narrow screens without breaking layout.

### 2. Stats — `github-readme-stats` service (existing)

Two cards in a centered flex row:
- **Main stats card:** total stars, commits, PRs, issues, contributed-to
- **Top languages card:** compact layout, top 5

**Changes from current README:**
- Switch theme from custom-color params to `theme=tokyonight` (cleaner, native cyan-leaning palette)
- Keep `hide_border=true`, `bg_color=00000000` (transparent so README dark theme shows through)
- Different `card_width` per card is intentional (320 for the longer-label stats card, 280 for the compact langs card); both render at `height=140` so they align as a row.

URL templates:
```
https://github-readme-stats.vercel.app/api?username=95-yn&theme=tokyonight&hide_border=true&bg_color=00000000&card_width=320&hide=contribs
https://github-readme-stats.vercel.app/api/top-langs/?username=95-yn&theme=tokyonight&hide_border=true&bg_color=00000000&card_width=280&layout=compact&langs_count=5
```

### 3. Snake — existing workflow

No changes. The current `.github/workflows/snake.yml` already generates the mono-cyan snake and publishes to `output` branch. The README image reference stays:
```
![snake](https://raw.githubusercontent.com/95-yn/95-yn/output/github-contribution-grid-snake-dark.svg)
```

### 4. Footer

Two centered lines:
- `─────────────── EOF ───────────────` (muted grey, decorative)
- `tuttikit · github.com/95-yn/tuttikit` (link, cyan accent on the URL)

No views counter (current README has one — drop it; it adds visual noise and provides little signal at low traffic).

### 5. Dividers — `assets/divider.svg`

A single reusable SVG, ~720×8px:
- Horizontal cyan line, `linear-gradient` fading from transparent → cyan → transparent
- An `<animate>` element creates a slow scan-light traveling along the line (8s loop, subtle)
- Used 2× in the README (between hero/stats and between stats/snake)

## File Layout in Repo

```
95-yn/95-yn/
├── README.md                ← rewritten
├── assets/
│   ├── hero.svg            ← new
│   └── divider.svg         ← new
├── .github/
│   └── workflows/
│       └── snake.yml       ← unchanged
├── .gitignore              ← added .superpowers/
└── docs/
    └── superpowers/
        └── specs/
            └── 2026-05-22-github-profile-cyber-terminal-design.md  ← this file
```

## README.md Final Structure (markdown sketch)

```markdown
<div align="center">

<img src="./assets/hero.svg" alt="95-yn · agents builder" width="760"/>

<img src="./assets/divider.svg" alt="" width="720"/>

<img src="https://github-readme-stats.vercel.app/api?username=95-yn&theme=tokyonight&hide_border=true&bg_color=00000000&card_width=320&hide=contribs" height="140"/>
<img src="https://github-readme-stats.vercel.app/api/top-langs/?username=95-yn&theme=tokyonight&hide_border=true&bg_color=00000000&card_width=280&layout=compact&langs_count=5" height="140"/>

<img src="./assets/divider.svg" alt="" width="720"/>

![snake](https://raw.githubusercontent.com/95-yn/95-yn/output/github-contribution-grid-snake-dark.svg)

<sub><code>tuttikit</code> · <a href="https://github.com/95-yn/tuttikit">github.com/95-yn/tuttikit</a></sub>

</div>
```

## Things Removed vs. Current README

| Element | Why removed |
|---|---|
| `readme-typing-svg` brand header | Replaced by static SVG hero with cursor animation (more reliable rendering, no external CDN dependency for the title) |
| `readme-typing-svg` tagline | Tagline text is baked into hero SVG |
| `komarev.com` profile views counter | Adds visual clutter; signal value is low |
| `const me = {}` TypeScript code block | Its info is absorbed into hero (`building`, `thinking`) — block became redundant |

## Open Items for User Confirmation Before Implementation

These will be confirmed before code is written:

1. **Hero tagline lines:** are these two lines the final copy?
   - `> building tuttikit — conductor + sub-agents`
   - `> agents that don't break when specs drift`
2. **Last login line wording:** confirm `Last login: today · session #00f0ff` or pick a variant.
3. **Footer project link:** is `tuttikit` still the right featured repo to point to?

## Out of Scope (Explicit Non-Goals)

- No light-mode variant — README assumes GitHub dark theme. (Currently true for >80% of viewers; not worth designing for the inverse.)
- No A/B variants of hero (e.g., per-visit randomization).
- No CI / lint of the SVGs.
- No accessibility audit beyond meaningful `alt` text on the hero `<img>`.
- No changes to `.github/workflows/snake.yml`.

## Success Criteria

- README on `github.com/95-yn` renders the three blocks in dark mode with no broken images.
- Hero SVG shows visible cyan glow on the box border, a slow blinking cursor, and faint scanlines.
- Layout does not horizontally scroll on a 375px-wide viewport.
- Removing any one of the three blocks would make the README feel sparse — confirming this is the minimum viable "cool" version, not yet trimmable further.
