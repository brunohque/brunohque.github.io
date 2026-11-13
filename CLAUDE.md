# CLAUDE.md

Guidance for AI assistants working in this repository.

## What this is

Bruno Ferreira's media kit: a single-page static site served by GitHub Pages at
https://brunohque.github.io. It is a user-pages repo (`<user>.github.io`), so the
default branch is published as the live site, from the repository root.

There is no build step, package manager, framework, test suite, linter or CI.
What is committed is exactly what is served.

## Layout

```
index.html    The whole site: markup, CSS (one <style> block) and JS (inline <script> blocks)
img/          Portrait and video thumbnails (JPEG)
logos/        Brand logos for the marquee (SVG / PNG / WebP)
.nojekyll     Disables Jekyll processing on GitHub Pages; keep it
README.md     One-line description
```

Everything lives in `index.html` (~600 lines). Do not split it into separate
CSS/JS files or introduce a build tool unless asked.

### Page sections, in order

| Section | Anchor / selector | Notes |
|---|---|---|
| Nav | `#nav` | Sticky; links to `#performance`, `#audience`, `#work`, `#projects` |
| Hero | `header.hero` | Name, lead, mailto button, portrait |
| Brands | `section.brands`, `#track` | Logo marquee |
| Performance | `#performance` | Three count-up stats |
| Audience | `#audience` | Gender/age rings, location bars (`#locs`), world map (`#map`) |
| Case studies | `#work` (`.cases-block`) | Three `a.case` cards on the blue band |
| Top content | `#rail` | Horizontal scroll rail of `a.clip` cards |
| Personal | `.personal` | Three `a.pc` cards |
| Projects | `#projects` | Four `article.proj` cards |
| Contact | `#contact` | Mailto button and social links |
| Player | `#player` | Modal dialog for in-page video playback |

### Scripts (bottom of `index.html`)

1. **Page behaviour**: nav border on scroll, marquee duplication, count-up
   numbers, `.reveal` scroll-in via `IntersectionObserver`, rail arrow buttons.
2. **Video player**: the `CASES` object, URL parsing and the modal.
3. **World map**: D3 + topojson-client + world-atlas, all loaded from jsDelivr
   with pinned versions. It fails silently if the CDN is unreachable.

External dependencies are only those three CDN scripts plus Google Fonts
(Geist, Geist Mono). Keep versions pinned.

## Development workflow

Preview locally with any static server from the repo root:

```sh
python3 -m http.server 8000
```

Opening `index.html` via `file://` mostly works, but use a server when checking
the map or video embeds.

There are no automated checks, so verify changes by loading the page: check
desktop and a narrow viewport (breakpoints at 900px, 760px and 420px), open one
card of each kind (`.case`, `.clip`, `.pc`) in the player, and look at the
browser console for errors.

Deployment is a push to the default branch. Nothing else is required.

## Conventions

### Code style

- CSS is compact: one rule per line, no spaces after `:` or `;`, grouped under
  `/* section */` comments in page order. Match it rather than reformatting.
- JS is ES5-style inside IIFEs: `var`, `function` expressions, no arrow
  functions, modules or dependencies beyond D3 for the map. Keep it that way
  for consistency.
- Two-space indentation in HTML. Long card elements are kept on a single line.
- Use the design tokens on `:root` (`--paper`, `--ink`, `--ink-2`, `--line`,
  `--sand`, `--orange`, `--blue`, `--blue-soft`, `--pad`, `--max`, `--ease`)
  rather than new hard-coded colours. The map script repeats a few hex values
  because D3 needs literals; keep those in sync with the tokens.
- Shared building blocks: `.wrap` (page width), `.mono` (small uppercase
  label), `.tag` (orange numbered chip), `.btn` / `.btn-dark` / `.btn-orange`,
  `.sec-head` (section title row), `.panel`, `.cm` (corner marks; needs four
  empty `<b>` children), `.reveal` (scroll-in animation).

### Accessibility and motion

- Every animation must respect `prefers-reduced-motion`; the CSS block at the
  end of the stylesheet and the `reduce` flag in JS handle this. New animated
  elements that start hidden need adding to that reduced-motion rule.
- Decorative images use `alt=""`; inline SVG icons use `aria-hidden="true"`.
- External links use `target="_blank" rel="noopener"`.
- The player is a focus-trapped `role="dialog"` that closes on Escape or
  backdrop click and restores focus to the triggering card.

### Data that appears in more than one place

Several numbers are duplicated and must be changed together:

- **Count-up stats**: the `data-count` attribute and the element's text
  (the text is the no-JS / reduced-motion fallback). Optional `data-suffix`.
- **Audience location**: the `<b>` percentage and `data-w` on each `#locs` row,
  and the `share` object in the map script, keyed by ISO 3166-1 numeric code
  as a zero-padded string (`'076'` Brazil, `'376'` Israel, `'840'` US,
  `'124'` Canada, `'380'` Italy, `'250'` France).
- **Top location value `45.2`**: hard-coded as the bar-width divisor in the
  first script and as the colour-scale domain in the map script. Update both
  if the leading country's share changes.
- **Rings**: the `conic-gradient` stops are cumulative percentages and must
  match the legend beside them.
- **Case study metrics**: shown on the card (`.nums`), in the `CASES` entry,
  and again in the Top content rail for the reels that appear in both.

### Video cards and the player

- Any `a.case`, `a.clip` or `a.pc` whose `href` matches a pattern in `parse()`
  opens in the modal instead of navigating. Supported forms:
  - `instagram.com/reel/<id>/`
  - `tiktok.com/@<user>/video/<id>`
  - `youtube.com/shorts/<id>`
- Cards remain real links, so modifier-clicks and no-JS still work. Keep the
  `href` pointing at the public post.
- Play badges and the "Watch & read the case study" label are injected by JS;
  do not add them to the markup.
- A case study write-up exists only when an `a.case` card's reel ID has a
  matching key in `CASES`. Fields: `kicker`, `title`, `goal`, `stats`
  (array of `[value, label]`, laid out three per row), `role`, `outcome`
  (`[heading, [paragraphs]]`).
- Most `CASES` strings are HTML-escaped, but `outcome` paragraphs and stat
  values are inserted as raw HTML (so `<strong>` works). Write entities there
  by hand and never put untrusted text in them.
- To support another platform, add a branch to `parse()` returning
  `{pf, id, src}` with an embeddable `src`.

### Assets

- Thumbnails are named `<platform>-<post id>.jpg` with prefix `ig-`, `tt-` or
  `yt-`, matching the ID in the post URL. Card images are cropped by CSS
  (`4/5` for cases and personal, `9/16` for the rail), so source images only
  need to be portrait.
- Logos go in `logos/`. A square mark sits next to a text label
  (`<img>` + name); a wordmark uses `class="word"`, an inline `height`, and
  carries the brand name in `alt` instead.
- Add new marquee logos once, inside `#track`. The script duplicates the track
  for the seamless loop and hides the copy from assistive tech.
- All paths are relative (`img/…`, `logos/…`); keep them relative.
- Currently unreferenced by the page: `img/bruno.prev.jpeg`,
  `logos/israel-is-mark.png`, `logos/triips-mark.png`, and the `.placeholder`
  CSS class. Leave them unless asked to clean up.

### Copy

- British spelling (`localisation`, `personalised`).
- First person, plain and specific; section headings are short and end with a
  full stop ("Reach, at a glance.").
- Metrics are abbreviated on cards (`774K`, `4.9K`) and written in full in the
  headline stats (`4,425,272`).
- All figures, brand names and project descriptions are real claims about a
  real person's work. Never invent, round up or "refresh" numbers, brands or
  results; change them only to values the owner supplies.
- The contact email and social handles appear in several places (nav, hero,
  performance, contact). Update every occurrence together.

## Git

- Commit messages are short, imperative, sentence case, no prefix
  (e.g. "Play videos in-page and show case study write-ups").
- Pushing to the default branch publishes immediately, so do work on a branch
  and merge only when the owner asks.
