# BRAND-GUIDE.md
# Owens OnLine — Visual Identity & Interaction Standards
# Source of Truth: /context/color-palette.json v1.5
# Version: 1.0 | 2026-03-30

---

## 1. Brand Identity

### Logo & Typography

| Role | Specification |
|---|---|
| **Logo Typeface** | Times New Roman — used exclusively for the "Owens OnLine" wordmark |
| **Interface Typeface** | Neue Haas Grotesk Text Pro — all UI, navigation, body, labels |
| **Primary Brand Blue** | `#083B78` — the anchor color for all static brand expression |

**Typeface rule:** Never use Times New Roman in the UI. Never use Neue Haas Grotesk in the logo. These two typefaces occupy completely separate layers of the identity.

---

## 2. Color System

### 2.1 Hierarchy of Blue

Three blues. Each has a distinct job. They are not interchangeable.

| Token | Hex | Role | When to Use |
|---|---|---|---|
| **Brand Blue** | `#083B78` | Static identity | Logo, navigation text, section headers, footer brand expressions, icon fills at rest |
| **Interactive Blue** | `#124BC4` | Hover & highlight | Nav item hover states, text links, in-line text highlights on light backgrounds, light-bg CTAs |
| **Dark Background Blue** | `#2563EB` | Dark-surface action | CTAs on dark/navy sections, icon fills on dark backgrounds, text highlights on dark backgrounds |

**The hierarchy rule:** `#083B78` = authority and permanence. `#124BC4` = "you can interact with this." `#2563EB` = action on dark surfaces. A designer who swaps `#083B78` and `#124BC4` breaks the interactive signal — users lose the affordance cue.

---

### 2.2 Surface Colors

| Token | Hex | Role |
|---|---|---|
| **Header Background** | `#FFFFFF` | Navigation bar surface — pure white |
| **Page Background** | `#F8FAFC` | All content area backgrounds — off-white gray |
| **Light Divider** | `#CBCFD3` | Horizontal rules, card borders, table row dividers on light surfaces |
| **Dark Divider** | `#637488` | Dividers and borders on dark/navy section backgrounds |

**Critical distinction:** Header (`#FFFFFF`) and page (`#F8FAFC`) are intentionally different. This 8-value lightness gap is not accidental — it powers the Connected Tab navigation pattern (see Section 3).

---

### 2.3 Typography Colors

#### On Light Backgrounds (`#F8FAFC` / `#FFFFFF`)

| Role | Hex | Usage |
|---|---|---|
| Main Content Heading | `#2A3133` | H1, H2, H3 — near-black, not pure black |
| Subheading / Paragraph | `#535455` | Body copy, captions, secondary labels |
| Text Highlights | `#124BC4` | Inline links, emphasized terms, interactive text |

#### On Dark Backgrounds (navy, `#083B78` surfaces)

| Role | Hex | Usage |
|---|---|---|
| Main Content Heading | `#FFFFFF` | Headings on dark sections |
| Subheading / Paragraph | `#FFFFFF` | Body copy on dark sections — same value, weight/opacity controls hierarchy |
| Text Highlights | `#2563EB` | Interactive text on dark surfaces |

---

### 2.4 Action Elements

| Token | Hex | Role |
|---|---|---|
| **Find Products Accent** | `#D8272B` | The "Find Products" button only — red signals urgency/discovery, not danger |
| **CTA — Light Background** | `#124BC4` | Primary buttons on white/gray surfaces |
| **CTA — Dark Background** | `#2563EB` | Primary buttons on dark/navy surfaces |
| **Icon — Dark Background** | `#2563EB` | Icon fills on dark sections |
| **Icon Shade — Neutral** | `#D4D4D8` | Inactive icon fills, decorative icon tints |

**Find Products rule:** `#D8272B` is reserved exclusively for the "Find Products" navigation element. Do not use it for error states, alerts, or any other UI purpose — its meaning is "discovery entry point," not "warning."

---

## 3. The Connected Tab Navigation Pattern

### 3.1 Concept

The header sits on `#FFFFFF` (white). The page content area sits on `#F8FAFC` (off-white). These two surfaces are visually distinct. The Connected Tab pattern exploits this gap: when a navigation item is **active**, its background shifts from `#FFFFFF` to `#F8FAFC` — matching the content area below it. The active tab appears to merge with the page content, creating a "tab physically connected to its panel" mental model without any borders, shadows, or complex visual tricks.

```
┌─────────────────────────────────────────────────────────┐
│  HEADER  #FFFFFF                                        │
│                                                         │
│   [Home]  [Countries]  [PRODUCTS ▓▓▓]  [Resources]     │
│                         ▓ = #F8FAFC ▓                   │
│                         ▓ 3px #083B78▓                  │
└─────────────────────────────────────────────────────────┘
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
  PAGE CONTENT AREA  #F8FAFC
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
```

The active tab's bottom edge disappears visually because its background matches the content below it. The top border is the only visible indicator of state.

---

### 3.2 Navigation Item States

#### Default State (Inactive)
```
Background:   #FFFFFF  (matches header — item is "part of the bar")
Text:         #083B78  (Brand Blue — static, authoritative)
Top border:   none
Bottom edge:  visually flush with header bar
```

#### Hover State
```
Background:   #FFFFFF  (header background unchanged)
Text:         #124BC4  (Interactive Blue — signals "you can click this")
Top border:   none
Cursor:       pointer
Transition:   color 150ms ease
```

#### Active / Current Page State
```
Background:   #F8FAFC  (matches page content area — "connected to content below")
Text:         #083B78  (Brand Blue — same as default; location is communicated by bg, not color)
Top border:   3px solid #083B78  (Brand Blue — the single visual anchor for active state)
Bottom edge:  seamless merge with page background
```

#### Active + Hover State
```
Background:   #F8FAFC  (unchanged — already active)
Text:         #124BC4  (Interactive Blue — sub-items still interactive)
Top border:   3px solid #083B78  (unchanged)
```

---

### 3.3 CSS Implementation Reference

```css
/* Navigation item — base */
.nav-item {
  background: #FFFFFF;
  color: #083B78;
  border-top: 3px solid transparent; /* Reserve space — prevents layout shift on activate */
  padding: 16px 16px 13px; /* 16px top, 13px bottom = compensates for 3px top border */
  font-family: 'Neue Haas Grotesk Text Pro', sans-serif;
  text-decoration: none;
  transition: color 150ms ease, background 150ms ease;
}

/* Hover */
.nav-item:hover {
  color: #124BC4;
}

/* Active / current page */
.nav-item.active,
.nav-item[aria-current="page"] {
  background: #F8FAFC;
  color: #083B78;
  border-top-color: #083B78;
}

/* Active + hover (sub-items still interactive) */
.nav-item.active:hover {
  color: #124BC4;
}

/* Header bar */
.site-header {
  background: #FFFFFF;
  border-bottom: 1px solid #CBCFD3; /* Light divider — separates header from page on non-active sections */
}

/* Page content area */
.page-content {
  background: #F8FAFC;
}
```

**Critical implementation note:** `border-top: 3px solid transparent` on the default state is required to reserve the 3px space. Without it, activating a tab adds 3px and causes a layout shift that pushes all nav items down on interaction.

---

### 3.4 ⚑ Audit Gate — Visual Anchor Check

**Check name:** Connected Tab Visual Continuity

**Test:** Does the active navigation item's background (`#F8FAFC`) visually merge with the page content background (`#F8FAFC`) below the header?

**Pass condition:** Active tab background = `#F8FAFC`. Top border = 3px `#083B78`. Bottom edge of tab disappears into content area — no visible gap, shadow, or border separating tab from content.

**Fail conditions:**

| Failure | Symptom | Fix |
|---|---|---|
| Active bg ≠ `#F8FAFC` | Tab appears "floating" — does not connect to content | Set active bg to `#F8FAFC` |
| Top border absent | Active state indistinguishable from hover state | Add `border-top: 3px solid #083B78` |
| Top border wrong color | Brand consistency break | Set to `#083B78` only — not `#124BC4` or any other blue |
| Header `border-bottom` bleeds under active tab | Visible line cuts through the connection | Remove or mask `header border-bottom` behind active tab element |
| Page bg ≠ `#F8FAFC` | Connection breaks because surfaces no longer match | Verify page content area is `#F8FAFC` — not `#FFFFFF` or any other value |

**Flag in design audits:** Any mockup where the active nav item does not visually connect to the main content background is a Connected Tab failure. Treat as a 🟡 Flag in design audits — it does not break usability but breaks the designed mental model and reduces perceived information architecture clarity.

---

## 4. Service Pillar Color System

### 4.1 The 8 Service Pillar Colors

Each service category has a designated accent color. These colors exist to create **Color Scent** — a visual navigation aid that lets repeat users recognize service categories across pages before reading the label.

| Service Pillar | Hex | Swatch |
|---|---|---|
| **Credit & Finance** | `#BCA036` | Gold / dark amber |
| **Civil Records** | `#4981CE` | Medium blue |
| **Criminal Records** | `#22316C` | Deep navy |
| **Due Diligence & Media** | `#A2908E` | Muted rose-gray |
| **Driving & Motor Vehicle** | `#009994` | Teal |
| **Property & Assets** | `#1E910F` | Green |
| **Identity & Credentials** | `#731B92` | Purple |
| **Verifications & References** | `#F69F3B` | Orange |

---

### 4.2 Color Scent Application Rules

**Where to use service pillar colors:**

| Element | Application |
|---|---|
| **Accent bar** | 4px left-border on service cards — `border-left: 4px solid [pillar-color]` |
| **Icon background** | Soft tint fill behind service category icons — use at 12–15% opacity of pillar color |
| **Category label chip** | Small pill badge on product listing pages — `background: [pillar-color]` at 10% opacity, `color: [pillar-color]` at 100% |
| **Section anchor marker** | Thin left rule on content sections dedicated to a specific service |

**Where to NOT use service pillar colors:**

| Element | Reason |
|---|---|
| Navigation header | Keeps the white header clean and scannable — pillar colors belong to content, not chrome |
| Primary CTAs | CTAs use `#124BC4` (light bg) or `#2563EB` (dark bg) exclusively — mixing pillar colors into CTAs creates visual noise and weakens the CTA hierarchy |
| Body copy | Never use pillar colors as text color — contrast ratios are not guaranteed against all backgrounds, and it undermines the typography color system |
| Hero headlines | Brand Blue (`#083B78`) or heading near-black (`#2A3133`) only |

---

### 4.3 Color Scent in Practice

**Correct:** A Criminal Records service card has a `4px solid #22316C` left border. The icon behind the gavel symbol has a `rgba(34, 49, 108, 0.12)` background fill. The category label reads "Criminal Records" in `#22316C` text on a `rgba(34, 49, 108, 0.08)` chip background.

**Incorrect:** The navigation "Products" dropdown uses `#22316C` as a background for the Criminal Records menu item. The service pillar color has leaked into the header chrome — this is a Color Scent failure.

**The rule in one sentence:** Service pillar colors live in the content area. The header lives in Brand Blue (`#083B78`) and white.

---

## 5. Quick Reference Card

```
BRAND BLUE       #083B78   Static nav text, logo, headers, top border on active tab
INTERACTIVE BLUE #124BC4   Hover states, text links, light-bg CTAs, text highlights
DARK ACTION BLUE #2563EB   CTAs on dark surfaces, icons on dark, text highlights on dark
FIND PRODUCTS    #D8272B   "Find Products" button ONLY
HEADING          #2A3133   H1–H3 on light backgrounds
BODY TEXT        #535455   Paragraphs, captions on light backgrounds
HEADER BG        #FFFFFF   Navigation bar surface
PAGE BG          #F8FAFC   All content area — also: active tab background
LIGHT DIVIDER    #CBCFD3   Borders, rules, card outlines on light surfaces
DARK DIVIDER     #637488   Borders, rules on dark/navy surfaces
```

```
SERVICE PILLARS (accent bars + icon fills in content area only)
Credit & Finance    #BCA036   Civil Records       #4981CE
Criminal Records    #22316C   Due Diligence       #A2908E
Driving & Motor     #009994   Property & Assets   #1E910F
Identity/Credentials#731B92   Verifications       #F69F3B
```

---

## 6. Audit Checklist — Brand Compliance

Use in Type 1, Type 1B, and Type 3 audits to verify brand implementation.

- [ ] Logo uses Times New Roman — no other typeface
- [ ] UI uses Neue Haas Grotesk Text Pro — not Times New Roman
- [ ] Navigation text at rest: `#083B78`
- [ ] Navigation text on hover: `#124BC4` — not `#083B78`, not `#2563EB`
- [ ] Active nav item background: `#F8FAFC` — matches page background
- [ ] Active nav item top border: `3px solid #083B78`
- [ ] Active tab visually connects to content area (Visual Anchor Check — see Section 3.4)
- [ ] Header background: `#FFFFFF` — not `#F8FAFC`
- [ ] Page content background: `#F8FAFC` — not `#FFFFFF`
- [ ] `#D8272B` used only for "Find Products" — not in error states, not in other CTAs
- [ ] Service pillar colors confined to content area — not in header chrome
- [ ] Body text on light bg: `#535455` — not pure black
- [ ] Heading text on light bg: `#2A3133` — not pure black

---

**Version:** 1.0 | **Source:** `/context/color-palette.json v1.5` | **Project:** Owens OnLine FE2 Redesign
