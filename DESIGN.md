# Design System — disce-practice

Reference for keeping every quiz in this repo visually consistent. Theme:
a classical, inscribed-tablet feel — ink, parchment, and bronze — instead of
a generic app look.

## Color palette

| Token           | Hex       | Used for |
|-----------------|-----------|----------|
| `--ink`         | `#1C2333` | Page background, header banner, button fill |
| `--ink-2`       | `#262E45` | Button hover state |
| `--parchment`   | `#F3ECDD` | Card / sheet background |
| `--parchment-2` | `#FBF7EE` | Input fields, scoreboard bar, footer |
| `--gold`        | `#C9A15B` | Eyebrow text, header bottom border |
| `--gold-deep`   | `#A9793E` | Borders, numerals, focus outlines |
| `--text-ink`    | `#2A2117` | Body text |
| `--green-text`  | `#2F6B4F` | "ITA" (correct) text |
| `--green-fill`  | `#E3EEE4` | "ITA" badge background |
| `--green-ring`  | `#4F8E6C` | "ITA" badge border |
| `--red-text`    | `#8A2E22` | "NON" (incorrect) text |
| `--red-fill`    | `#F5E1DB` | "NON" badge background |
| `--red-ring`    | `#B4543F` | "NON" badge border |
| `#DCD0B2`       | —         | Grid hairlines between cards |
| `#E6DCC3`       | —         | Header subtext (lighter than parchment for contrast on ink) |
| `#6B5E44`       | —         | Field labels ("YOUR ANSWER") |

All text/background pairings were checked against WCAG contrast minimums
(4.5:1 for normal text) and pass comfortably — most are 6:1 or higher. If you
introduce a new color, check it before shipping:
`contrast = (L1 + 0.05) / (L2 + 0.05)` using relative luminance.

## Typography

Three font roles, all system-font stacks (no external font loading, so the
page works completely offline):

| Role | Stack | Where |
|------|-------|-------|
| Body / reading | `"Palatino Linotype", "Book Antiqua", Palatino, Georgia, serif` | Base body text |
| Display | `Georgia, "Times New Roman", serif` | Page title (uppercase), Roman numerals, seal badge letters |
| UI / labels | `"Trebuchet MS", "Segoe UI", Verdana, sans-serif` | Eyebrow, buttons, field labels, scoreboard, footer key |

The title uses `text-transform: uppercase` with `letter-spacing: 0.03em` to
read like an inscription. Latin words in each card are `italic`, `bold`,
`19px`.

## Layout

- Outer `.sheet` container: `max-width: 920px`, centered, thin gold-deep
  border, plus a 6px ink "frame" via `box-shadow` and a soft drop shadow.
- Page background: solid ink navy with two faint radial gradients (`#232B41`)
  for subtle depth — no image assets.
- Structure, top to bottom: **header banner** (ink bg) → **scoreboard bar**
  (parchment-2 bg, live tally + reset button) → **question grid** → **footer
  key** (parchment-2 bg, explains the ITA/NON indicators).
- Question grid: CSS Grid, `repeat(auto-fit, minmax(360px, 1fr))` — this is
  what makes it responsive (2 columns on desktop, 1 on narrow/mobile)
  without a media query. Grid lines are done via a colored `gap` background
  trick (`gap: 1px; background: #DCD0B2`) rather than per-cell borders, so it
  stays correct at any column count.
- Each question card: Roman numeral + italic word, then an answer row
  (label + input, Check button, result badge) that wraps naturally on
  narrow screens via `flex-wrap`.

## The ITA / NON pattern

This is the signature interactive element — reuse it exactly as-is for any
new quiz type (fill-in-the-blank, matching, etc.):

- A 44px circular badge (26px in the footer legend) that's invisible
  (`opacity: 0; scale(0.6)`) until a "show" class is added, then springs in
  with a bounce (`cubic-bezier(.34,1.56,.64,1)`).
- **Never color alone.** Every result pairs three signals: color (green/red),
  an icon (checkmark / X, inline SVG), and text (`ITA` / `NON`). This keeps
  it usable for colorblind students.
- Respects `prefers-reduced-motion` by disabling the pop-in transition.

## Accessibility baseline

Carry these forward on every new quiz:

- Every `<input>` has a real `<label for="">`, not a placeholder-only field.
- Result badges use `role="status" aria-live="polite"` so screen readers
  announce ITA/NON automatically.
- Visible `:focus-visible` outlines (gold-deep, 3px) on every interactive
  element — never rely on the browser default alone.
- Enter key submits an answer, same as clicking Check.
- No color-only signal anywhere (see ITA/NON pattern above).
- Contrast checked, not assumed (see palette table).

## Reuse checklist for a new chapter

1. Copy the most recent `.html` file, rename it.
2. Edit only the `VERBS` array and the `<header>` text (eyebrow, `<h1>`,
   instructions paragraph).
3. If the exercise type changes (e.g. matching instead of fill-in-the-blank),
   keep the palette, fonts, `.sheet` frame, scoreboard, and ITA/NON pattern
   identical — only the question-card markup should change.
4. Re-check contrast if you add any new color.
5. Add a row to the Quizzes table in `README.md`.
