# CLAUDE.md

Guidance for Claude Code when working in this repo.

## About this repo

Bruno Ferreira's media kit: a static site served by GitHub Pages at https://brunohque.github.io.

- `index.html` is the whole site (HTML, CSS and JS in one file). There is no build step, bundler or package manager.
- `img/` holds photos and social thumbnails. `logos/` holds brand logos.
- External dependencies load from CDNs (Google Fonts, d3, topojson-client) with pinned versions.
- `.nojekyll` must stay, so GitHub Pages serves files as-is.
- Anything pushed to the deployed branch goes live. This is a public portfolio, so copy and visuals must stay polished.

## Working rules

### 1. Think before coding
- State assumptions. If a request is ambiguous, ask or present the options. Don't pick silently.
- If a simpler approach exists, say so.

### 2. Simplicity first
- Make the minimum change that solves the request. No extra features, abstractions or configurability.
- Keep it a single `index.html` with no build tooling unless asked.

### 3. Surgical changes
- Touch only what the request needs. Match the existing style (CSS variables in `:root`, class naming, indentation).
- Don't reformat or "improve" adjacent code. Mention unrelated problems instead of fixing them.
- Remove only the leftovers your own change creates.
- Don't rename or delete files in `img/` or `logos/`. `index.html` references them by path.
- Don't change Bruno's copy, stats or client claims unless asked. They are real business information.

### 4. Goal-driven execution
- Turn the task into a checkable goal and verify it before saying it's done.
- For visual changes, render the page (Playwright is available in cloud sessions) and check it at phone width (~390px) and desktop width. Bruno often reviews on an iPad.
- Keep the page working with no console errors and no broken image links.

## Git
- Work on the branch given for the session. Don't push elsewhere without asking.
- Don't open pull requests unless asked.
