# GitHub Profile Cyber-Terminal Redesign — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the current `95-yn/95-yn` profile README with a "cyberpunk terminal" version — custom motd-style hero SVG, tokyonight stats, existing snake — implemented across 2 new SVG assets + a README rewrite.

**Architecture:** All visual flair lives in 2 self-contained SVG files under `assets/`. The README is a thin markdown file that composes those SVGs with two existing third-party services (github-readme-stats, Platane/snk). No new GitHub Actions, no build step, no runtime code. Each SVG includes its own animation via SMIL `<animate>` elements and a CSS-style `<filter>` for cyan glow.

**Tech Stack:** Pure SVG 1.1 (with SMIL animation), GitHub-flavored Markdown, github-readme-stats (`tokyonight` theme), existing `Platane/snk` workflow.

**Pre-flight:**
- Working directory for all tasks: `~/projects/95-yn-profile/`
- Spec reference: `docs/superpowers/specs/2026-05-22-github-profile-cyber-terminal-design.md`
- Current branch is `main` and is 1 commit ahead of `origin/main` (the spec commit). Do all work on a feature branch.

---

### Task 1: Create feature branch and assets directory

**Files:**
- Create: `assets/` (directory)

- [ ] **Step 1: Create and switch to feature branch**

```bash
cd ~/projects/95-yn-profile
git checkout -b redesign/cyber-terminal
```

Expected: `切换到一个新分支 'redesign/cyber-terminal'` (or English equivalent).

- [ ] **Step 2: Create assets directory**

```bash
mkdir -p assets
```

- [ ] **Step 3: Confirm directory exists**

```bash
ls -la assets/
```

Expected: empty directory, exists.

No commit yet — empty directory has nothing to commit.

---

### Task 2: Build the hero SVG

**Files:**
- Create: `assets/hero.svg`

This is the centerpiece. A motd-style terminal welcome box with:
- Cyan border with soft outer glow (via `<filter>` with `feGaussianBlur`)
- Box-drawing-character frame around the content
- Three text blocks: login banner / brand line / two tagline lines
- A blinking block cursor at the end of the tagline (SMIL `<animate>` toggling opacity)
- Faint scanline pattern overlay (a `<pattern>` of repeating horizontal lines at low cyan opacity)

The SVG uses `viewBox="0 0 760 280"` so it scales on narrow screens. Text is monospace via the SVG `font-family` attribute (no embedded webfont — relies on viewer's system mono font, which is fine for all major OSes).

- [ ] **Step 1: Write `assets/hero.svg`**

```xml
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 760 280" width="760" height="280" role="img" aria-label="95-yn — agents builder. Building tuttikit, a conductor and sub-agents framework.">
  <defs>
    <!-- Cyan outer glow for the border box -->
    <filter id="glow" x="-20%" y="-20%" width="140%" height="140%">
      <feGaussianBlur stdDeviation="2.5" result="blur"/>
      <feMerge>
        <feMergeNode in="blur"/>
        <feMergeNode in="SourceGraphic"/>
      </feMerge>
    </filter>
    <!-- Stronger glow for the brand text -->
    <filter id="textGlow" x="-20%" y="-20%" width="140%" height="140%">
      <feGaussianBlur stdDeviation="1.6" result="b"/>
      <feMerge>
        <feMergeNode in="b"/>
        <feMergeNode in="SourceGraphic"/>
      </feMerge>
    </filter>
    <!-- Faint scanline overlay -->
    <pattern id="scan" x="0" y="0" width="4" height="4" patternUnits="userSpaceOnUse">
      <rect width="4" height="2" fill="#00f0ff" opacity="0.025"/>
    </pattern>
  </defs>

  <!-- Background panel -->
  <rect x="0" y="0" width="760" height="280" rx="8" fill="#161b22"/>

  <!-- Glowing border -->
  <rect x="1" y="1" width="758" height="278" rx="8"
        fill="none" stroke="#00f0ff" stroke-opacity="0.5" stroke-width="1"
        filter="url(#glow)"/>

  <!-- Scanline overlay (above bg, below text) -->
  <rect x="0" y="0" width="760" height="280" rx="8" fill="url(#scan)"/>

  <!-- Content. All text uses system monospace. -->
  <g font-family="'JetBrains Mono', 'SF Mono', Menlo, Consolas, monospace" font-size="14">

    <!-- Top frame: ┌─ ssh ... ─┐ -->
    <text x="32" y="44" fill="#6e7681">┌─ ssh 95-yn@github.com ─────────────────────────────┐</text>

    <!-- Last login line -->
    <text x="32" y="74" fill="#6e7681">│</text>
    <text x="56" y="74" fill="#00f0ff">$</text>
    <text x="72" y="74" fill="#8b949e">Last login: today · session #00f0ff</text>

    <!-- Spacer -->
    <text x="32" y="100" fill="#6e7681">│</text>

    <!-- Brand line: 95-yn // agents builder -->
    <text x="32" y="138" fill="#6e7681">│</text>
    <text x="80" y="146" fill="#00f0ff" font-size="28" font-weight="700" filter="url(#textGlow)">95-yn</text>
    <text x="190" y="146" fill="#6e7681" font-size="14">// agents builder</text>

    <!-- Spacer -->
    <text x="32" y="178" fill="#6e7681">│</text>

    <!-- Tagline 1 -->
    <text x="32" y="206" fill="#6e7681">│</text>
    <text x="56" y="206" fill="#00f0ff">&gt;</text>
    <text x="72" y="206" fill="#c9d1d9">building </text>
    <text x="138" y="206" fill="#d2a8ff">tuttikit</text>
    <text x="200" y="206" fill="#c9d1d9">— conductor + sub-agents</text>

    <!-- Tagline 2 + blinking cursor -->
    <text x="32" y="232" fill="#6e7681">│</text>
    <text x="56" y="232" fill="#00f0ff">&gt;</text>
    <text x="72" y="232" fill="#c9d1d9">agents that don't break when specs drift</text>
    <rect x="370" y="220" width="10" height="16" fill="#00f0ff">
      <animate attributeName="opacity" values="1;1;0;0" keyTimes="0;0.5;0.5;1" dur="1.1s" repeatCount="indefinite"/>
    </rect>

    <!-- Bottom frame -->
    <text x="32" y="262" fill="#6e7681">└─────────────────────────────────────────────────────┘</text>
  </g>
</svg>
```

- [ ] **Step 2: Visually inspect by opening the SVG in a browser**

```bash
open assets/hero.svg
```

Expected: a 760×280 dark panel with cyan glowing border, scanlines, the motd text, and a blinking cyan cursor at the end of the second tagline.

If the cursor overlaps text, adjust the `x` of the `<rect>` to land just after the last character of "drift". Eyeball it — exact pixel doesn't matter as long as it sits after the period/text.

- [ ] **Step 3: Commit**

```bash
git add assets/hero.svg
git commit -m "feat(hero): motd-style cyan terminal hero SVG"
```

---

### Task 3: Build the divider SVG

**Files:**
- Create: `assets/divider.svg`

A 720×8 SVG: horizontal cyan line with transparent → cyan → transparent gradient, plus a slow scan-light traveling across it on a loop.

- [ ] **Step 1: Write `assets/divider.svg`**

```xml
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 720 8" width="720" height="8" role="presentation" aria-hidden="true">
  <defs>
    <linearGradient id="line" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" stop-color="#00f0ff" stop-opacity="0"/>
      <stop offset="50%" stop-color="#00f0ff" stop-opacity="0.7"/>
      <stop offset="100%" stop-color="#00f0ff" stop-opacity="0"/>
    </linearGradient>
    <linearGradient id="scan" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" stop-color="#00f0ff" stop-opacity="0"/>
      <stop offset="50%" stop-color="#ffffff" stop-opacity="0.9"/>
      <stop offset="100%" stop-color="#00f0ff" stop-opacity="0"/>
    </linearGradient>
    <filter id="dGlow" x="-10%" y="-200%" width="120%" height="500%">
      <feGaussianBlur stdDeviation="1.5"/>
    </filter>
  </defs>

  <!-- Base gradient line -->
  <rect x="0" y="3" width="720" height="2" fill="url(#line)" filter="url(#dGlow)"/>

  <!-- Traveling scan highlight -->
  <rect x="-120" y="3" width="120" height="2" fill="url(#scan)" opacity="0.9">
    <animate attributeName="x" from="-120" to="720" dur="6s" repeatCount="indefinite"/>
  </rect>
</svg>
```

- [ ] **Step 2: Visually inspect**

```bash
open assets/divider.svg
```

Expected: a thin glowing cyan line. A brighter highlight slides left-to-right across it every 6 seconds.

- [ ] **Step 3: Commit**

```bash
git add assets/divider.svg
git commit -m "feat(divider): animated cyan scan-line divider SVG"
```

---

### Task 4: Rewrite README.md

**Files:**
- Modify: `README.md` (full rewrite — current content is the minimal mono-cyan version with typing-svg hero, code block, stats, snake)

The new README composes the two new SVGs with the existing stats service URLs (theme switched to `tokyonight`) and the existing snake image.

- [ ] **Step 1: Replace `README.md` with the new content**

Overwrite the entire file with:

```markdown
<div align="center">

<img src="./assets/hero.svg" alt="95-yn · agents builder · building tuttikit — agents that don't break when specs drift" width="760"/>

<img src="./assets/divider.svg" alt="" width="720"/>

<img src="https://github-readme-stats.vercel.app/api?username=95-yn&theme=tokyonight&hide_border=true&bg_color=00000000&card_width=320&hide=contribs" height="140"/>
<img src="https://github-readme-stats.vercel.app/api/top-langs/?username=95-yn&theme=tokyonight&hide_border=true&bg_color=00000000&card_width=280&layout=compact&langs_count=5" height="140"/>

<img src="./assets/divider.svg" alt="" width="720"/>

![snake](https://raw.githubusercontent.com/95-yn/95-yn/output/github-contribution-grid-snake-dark.svg)

<sub><code>tuttikit</code> · <a href="https://github.com/95-yn/tuttikit">github.com/95-yn/tuttikit</a></sub>

</div>
```

- [ ] **Step 2: Inspect locally with a markdown previewer**

You can use any of:

```bash
# Option A: GitHub-style preview via `gh` CLI (if available)
gh markdown-preview README.md   # opens browser

# Option B: open the file in VS Code and use its built-in preview
code README.md   # then Cmd+Shift+V

# Option C: just verify the structure manually
cat README.md
```

Expected: file has exactly the structure above, no leftover sections from the old README, and the two `<img src="./assets/...">` paths point at the files you created.

- [ ] **Step 3: Commit**

```bash
git add README.md
git commit -m "feat: rewrite README in cyberpunk-terminal style"
```

---

### Task 5: Push branch and visually verify on GitHub

**Files:** none

GitHub renders SVG via `<img>` tag, but to see it the SVG must be reachable via a public URL. The README references the SVGs by relative path (`./assets/hero.svg`), which GitHub resolves against the current branch. We push the branch and verify on GitHub before merging to `main`.

- [ ] **Step 1: Push the feature branch**

```bash
git push -u origin redesign/cyber-terminal
```

Expected: branch created on remote, push succeeds. Output includes a "create a pull request" URL like `https://github.com/95-yn/95-yn/pull/new/redesign/cyber-terminal`.

- [ ] **Step 2: Open the branch's README on GitHub in a browser**

```bash
open "https://github.com/95-yn/95-yn/tree/redesign/cyber-terminal"
```

This shows the README rendered as GitHub will render it on the profile, but on a feature branch (so it doesn't affect your live profile yet).

- [ ] **Step 3: Verify the rendered result against the success criteria**

Check each:

1. Hero SVG appears with a visible cyan border (with soft glow). Border drawing characters are aligned. Brand text "95-yn" is large and cyan with a glow halo.
2. Blinking cursor at the end of "agents that don't break when specs drift" is animating.
3. Faint scanlines visible across the hero (subtle — they should be barely there, not a TV-static effect).
4. Two stats cards render side-by-side in `tokyonight` palette with a transparent background.
5. Both dividers render as a thin cyan line. (Animation may or may not visibly play in GitHub's image rendering — see note below.)
6. Snake image still loads from `output` branch.
7. On a mobile viewport (open Chrome devtools, narrow to ~375px), nothing horizontally overflows the column.

**Note on SVG animation in GitHub:** GitHub strips `<script>` from inline SVGs, but `<animate>` (SMIL) is generally preserved in `<img>`-referenced SVGs. The hero cursor and divider scan-highlight should animate. If GitHub later changes this and they don't animate, the visual still works as a static design — no fallback needed.

- [ ] **Step 4: If anything looks wrong, fix and re-push**

Common fixes:
- Mis-aligned box-drawing characters in hero → adjust `x` values in the corresponding `<text>` elements in `assets/hero.svg`, save, commit, push.
- Hero too tall/short on the page → adjust `width="760"` on the `<img>` in `README.md`.
- Stats cards misaligned → tweak `card_width` query param on either URL.

Commit any fixes with a message describing the tweak (e.g., `fix(hero): align box-drawing rightmost edge`), then re-push.

---

### Task 6: Merge to main

**Files:** none

Profile READMEs are served from the default branch (`main`). Once the feature branch looks right on GitHub, merge it.

- [ ] **Step 1: Switch to main and merge**

```bash
git checkout main
git merge --no-ff redesign/cyber-terminal -m "merge: cyberpunk-terminal README redesign"
```

Expected: clean merge (the only changes are the spec commit on main + new commits on the branch, no conflicts).

- [ ] **Step 2: Push main**

```bash
git push origin main
```

- [ ] **Step 3: Verify on the live profile**

```bash
open "https://github.com/95-yn"
```

Expected: profile page renders the new README. Cursor blinks, dividers visible, stats present, snake animating.

- [ ] **Step 4: Delete the feature branch (local and remote)**

```bash
git branch -d redesign/cyber-terminal
git push origin --delete redesign/cyber-terminal
```

---

## Notes for the implementing engineer

- **GitHub caches SVGs aggressively via Camo** (its image proxy). If you re-push the same SVG and don't see the update, append a cache-buster query to the `<img src>` like `./assets/hero.svg?v=2`. Bump on each update.
- **Don't introduce `<script>` into the SVGs** — GitHub strips it. Stick to SMIL `<animate>` for motion.
- **The `output` branch is managed by `.github/workflows/snake.yml`** — do not touch it manually. It regenerates daily and on push to main.
- **There is no test framework here** (it's a markdown + SVG repo). "Tests" in this plan are visual: open the file, open the rendered GitHub URL, check it looks right.
- **If a tagline word later changes** (e.g., user wants different copy), edit the corresponding `<text>` in `assets/hero.svg` — text is in the source, not encoded.
