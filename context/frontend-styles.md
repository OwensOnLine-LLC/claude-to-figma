# frontend-styles.md
# Reference: CSS/HTML boilerplate from audit-template.html
# Use when injecting content into audit-template.html

## CSS Custom Properties (Design Tokens)
```css
:root {
  --ink: #0d1117;        /* Primary dark — banner bg, section headers */
  --ink-mid: #1c2333;    /* Score strip bg */
  --blue: #1a56db;       /* Primary accent — section number circles, links */
  --green: #16a34a;      /* Pass color */
  --green-soft: #dcfce7; /* winner-cell bg */
  --gray: #64748b;       /* Secondary text */
  --gray-light: #f1f5f9; /* Page bg */
  --border: #e2e8f0;     /* Cell borders */
  --col-a: #1e3a6e;      /* Score strip group A border (blue) */
  --col-b: #7c2d12;      /* Score strip group B border (orange) */
  --col-c: #4c1d95;      /* Score strip group C border (purple) */
}
```

## Cell Semantic Classes (Three-Column Grid)
```css
.winner-cell  { background: #f0fdf4; border-left: 3px solid #16a34a; } /* Pass — green */
.loser-cell   { background: #fff8f8; border-left: 3px solid #dc2626; } /* Fail — red */
.improved-cell{ background: #eff6ff; border-left: 3px solid #3b82f6; } /* Flag — blue */
/* Default .cell = white background, no left border accent */
```
**Injection rule:** loser-cell = score ≤4 | improved-cell = score 5–6 | winner-cell = score ≥7

## Callout Box Classes
```css
.callout.g { background:#f0fdf4; border-left:4px solid #16a34a; color:#166534; } /* Good/pass */
.callout.r { background:#fef2f2; border-left:4px solid #dc2626; color:#7f1d1d; } /* Critical/fail */
.callout.y { background:#fffbeb; border-left:4px solid #f59e0b; color:#78350f; } /* Warning/advisory */
.callout.b { background:#eff6ff; border-left:4px solid #3b82f6; color:#1e3a8a; } /* Info/dev note */
```

## Metric Row States
```html
<div class="metric-row pass">  <!-- border-left: green -->
<div class="metric-row warn">  <!-- border-left: amber -->
<div class="metric-row fail">  <!-- border-left: red -->
```
```html
<span class="metric-value good"> <!-- #16a34a -->
<span class="metric-value ok">   <!-- #f59e0b -->
<span class="metric-value bad">  <!-- #dc2626 -->
```

## Priority Badges
```html
<span class="priority-badge priority-critical">🔴 N CRITICAL</span>
<span class="priority-badge priority-important">🟡 N IMPORTANT</span>
<span class="priority-badge priority-nice">🟢 N NICE-TO-HAVE</span>
```

## Score Chip States (Score Strip)
```css
.chip-score.s-great { color: #22c55e; } /* 8–10 */
.chip-score.s-good  { color: #84cc16; } /* 7–7.9 */
.chip-score.s-ok    { color: #eab308; } /* 5–6.9 */
.chip-score.s-bad   { color: #ef4444; } /* 4 or below */
```

## Page Structure Skeleton (Injection Order)
```
1. .banner → version-badge + track-badge + banner-label + h1 + p
2. .score-strip → 3 .score-groups with .score-chips
3. .page wrapper:
   a. .master-criterion (if applicable)
   b. .score-hero → .number + priority badges
   c. N × (.section-divider + .three-col)
   d. Final metrics dashboard (dark gradient block)
```

## Fonts
- Headings: `'Syne', sans-serif` (800 weight for h1, 700 for section headers)
- Body: `'Inter', sans-serif`
- Monospace (scores, code): `'JetBrains Mono', monospace`
