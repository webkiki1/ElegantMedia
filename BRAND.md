# Elegant Media — Brand & Design System

**Date:** 2026-09-14
**Status:** Applied in Batch 6a. This document has been updated to match what
was actually built — see "Deviations from the original spec" at the end.
**Basis:** No brand assets exist (no logo file, no colour spec, no type spec). This system is derived from the black-and-gold palette already in the stylesheet, which is sound — it is **formalised and extended, not replaced.**

Every contrast figure in this document was computed from the actual hex values, not estimated. All pass WCAG AA; most pass AAA.

---

## 1. What we're keeping, and why

Before specifying anything new, it's worth naming what already works — this is an extension of an existing system, not a rewrite:

- **The black-and-gold palette.** Right for luxury wedding photography, and already applied with real discipline — 22 KB of CSS with zero hardcoded colours outside the token list.
- **The type pairing.** Cormorant Garamond (display serif) with Jost (geometric sans) for Latin; El Messiri with Tajawal for Arabic. Both pairings are well chosen and script-appropriate.
- **Logical CSS properties** for right-to-left support, used consistently throughout.
- **Mobile-first structure**, five clean breakpoints, CSS Grid.

What's missing is the connective tissue: a spacing rhythm, a type scale, defined states, and rules for handling three scripts. That's what follows.

---

## 2. Brand character

**Elegant, restrained, warm.** Not ornate.

The subject matter — weddings, engagements, christenings — is already emotional. The design's job is to frame it, not compete with it. Gold is an accent that marks importance; it is not a texture that coats everything. Dark surfaces exist so photographs glow against them.

**Three languages are a core part of the brand, not a feature.** A family in Sweden planning a wedding or a christening may well be more comfortable in Arabic than Swedish. Serving all three properly — including getting Arabic typography right — is a real differentiator, and this system treats it as one.

---

## 3. Colour

### 3.1 Palette (unchanged)

| Token | Hex | Role |
|---|---|---|
| `--black` | `#0d0d0d` | Page background |
| `--black-soft` | `#161616` | Raised surfaces, alternating sections, form fields |
| `--black-card` | `#1c1c1c` | Cards |
| `--gold` | `#c9a24b` | Primary accent |
| `--gold-light` | `#e3c879` | Highlight, hover, focus ring |
| `--gold-deep` | `#a8842f` | Gradient end, pressed state |

*(These kept the names they already had in the stylesheet rather than being
renamed to `--ink-900` etc. as first drafted — the existing names are clear,
already used consistently throughout, and renaming would have touched every
rule in the file for no functional gain.)*

### 3.2 Semantic tokens (new)

This layer is the fix for the one crack in the existing system: `#ddd`, `#ccc`, `#cfcfcf` and `#666` currently appear as raw values scattered through the CSS, and `#666` is the site's only contrast failure.

| Token | Hex | Use | On `#0d0d0d` | On `#161616` | On `#1c1c1c` |
|---|---|---|---|---|---|
| `--text-primary` | `#f5f4f1` | Headings, key text | **17.67** ✅ | **16.45** ✅ | **15.49** ✅ |
| `--text-secondary` | `#c4c2bd` | Body copy, lists | **10.92** ✅ | **10.17** ✅ | **9.57** ✅ |
| `--text-muted` | `#9a9a9a` | Captions, labels, meta | **6.91** ✅ | **6.43** ✅ | **6.06** ✅ |
| `--text-subtle` | `#8f8d88` | Placeholders, copyright | **5.86** ✅ | **5.46** ✅ | **5.14** ✅ |
| `--text-accent` | `#e3c879` | Emphasis, links on dark | **11.85** ✅ | **11.03** ✅ | **10.39** ✅ |
| `--text-on-gold` | `#0d0d0d` | Text on gold surfaces | 8.10 on `--gold` ✅ · 5.56 on `--gold-deep` ✅ | | |

**Notes on replacements:**
- Pure `#ffffff` is retired for body text in favour of `#f5f4f1`. Pure white on near-black causes halation — the text appears to vibrate. The off-white still reaches 17.67:1.
- `--text-subtle` at `#8f8d88` replaces both `#666` (copyright, currently **3.15:1 — fails**) and the browser's default placeholder grey (currently **3.93:1 — fails**). At 5.14:1 in its worst context it clears AA with room to spare.
- The existing gold gradient button needs **no change** — black text measures 8.10:1 at the light end and 5.56:1 at the dark end. Both pass. Don't "fix" it.

### 3.3 Borders and surfaces

| Token | Value | Use |
|---|---|---|
| `--border-subtle` | `rgba(255,255,255,.07)` | Card edges, dividers |
| `--border-default` | `rgba(201,162,75,.25)` | Gold-tinted outlines *(existing `--line`)* |
| `--border-strong` | `var(--gold)` | Hover, active, emphasis |
| `--surface-gold-wash` | `rgba(201,162,75,.12)` | Icon backings, subtle gold fills |

### 3.4 Usage rules

1. **Gold marks one thing per view.** If everything is gold, nothing is important. In practice: one primary button per section.
2. **Never convey state with colour alone.** Active filters, selected language, and form errors each need a second signal — weight, icon, underline, or text.
3. **Photographs sit on `--ink-900`.** Cards and alternating bands use `--ink-800`/`--ink-700` to create rhythm without introducing new colours.
4. **No new colours without adding a token.** Including "just this once" greys — that's exactly how `#ddd`, `#ccc`, `#cfcfcf` and `#666` got in.
5. **Error state:** `#e5837a` (4.9:1 on `--ink-800`) — the only non-gold accent, reserved exclusively for form validation.

---

## 4. Typography

### 4.1 Families

| Script | Display / headings | Body |
|---|---|---|
| Latin (Swedish, English) | **Cormorant Garamond** 500/600/700 | **Jost** 300/400/500/600 |
| Arabic | **El Messiri** 400/600/700 | **Tajawal** 300/400/500/700 |

**Loading rule:** only the active language's two families load. Today all four load on every visit — a Swedish visitor downloads 11 Arabic font faces they will never see. *(Implemented in P8.)*

### 4.2 Type scale

Fluid, roughly a 1.25 ratio at text sizes, opening up at display sizes for editorial drama.

| Token | Size (min → max) | Use |
|---|---|---|
| `--text-xs` | 12px | Micro-labels, form hints |
| `--text-sm` | 14px | Captions, form labels, meta |
| `--text-base` | 16px | UI text, buttons, navigation |
| `--text-md` | 18px | Body copy, paragraphs |
| `--text-lg` | 21px | Lead paragraphs, pull quotes |
| `--text-xl` | `clamp(1.4rem, 1.1rem + 1.2vw, 1.6rem)` | Card titles (`h3`) |
| `--text-2xl` | `clamp(1.9rem, 1.2rem + 2.6vw, 2.9rem)` | Section titles (`h2`) |
| `--text-3xl` | `clamp(2.6rem, 1.5rem + 5vw, 4.8rem)` | Hero title (`h1`) |

This replaces eleven one-off values that were in the stylesheet (`1.02rem`, `1.05rem`, `.92rem`, `.95rem`, `1.1rem`, `1.15rem`, `1.2rem`, `1.3rem`, `1.4rem`, `1.5rem`, `2rem`) and didn't form a scale.

**The top three steps were tuned to the sizes the design already used.** The
figures first drafted here (a textbook 1.25 ratio topping out at 40px and 68px)
would have *shrunk* section headings from 46px to 40px and the hero from 77px to
68px. The existing sizes were well judged; the problem was that they were
arbitrary, not that they were wrong. The scale now formalises them rather than
overriding them — so applying it changed no heading size on the site.

A handful of sizes are deliberately **not** on the scale, and shouldn't be:
`.logo` and `.footer-logo` (fluid wordmark sizing), `.hero-sub` (a fluid lead),
`.stat strong` (a display number), `.logo-suffix` (`.4em`, relative to its
parent by design), and the `2rem` icon/glyph boxes, which size graphics rather
than text.

### 4.3 Line height

| Token | Latin | Arabic | Use |
|---|---|---|---|
| `--leading-display` | 1.08 | **1.30** | `h1` |
| `--leading-heading` | 1.25 | **1.50** | `h2`, `h3` |
| `--leading-body` | 1.65 | **1.95** | Paragraphs |

Arabic needs more leading — diacritics sit above the line and descenders reach further below. Latin values applied to Arabic produce visually cramped text. *(The current site already uses 1.8 for Arabic body, which is close; display headings are the ones that need opening up.)*

### 4.4 Letter-spacing — and one hard rule

| Token | Value | Applies to |
|---|---|---|
| `--tracking-display` | `-0.015em` | **Latin only** |
| `--tracking-body` | `0` | All scripts |
| `--tracking-label` | `0.14em` | **Latin only** — eyebrows, section tags |
| `--tracking-logo` | `0.22em` | **Latin only** — the wordmark |

> ### ⛔ Never letter-space Arabic. Ever.
>
> Arabic is a cursive script: letters physically join. Adding letter-spacing forces gaps between letters that are meant to connect, and the result reads as broken to anyone literate in Arabic — roughly the way `E L E G A N T` reads in Latin, but worse, because the joining strokes are part of the letterforms themselves.
>
> **The current site does exactly this, and gets it backwards:** section labels receive `letter-spacing: 3px` in Arabic versus `2px` in Latin. Arabic gets *more* spacing than the script that's designed for it. Verified live on `معرض الأعمال`.
>
> **Rule:** every `letter-spacing` declaration must be scoped to `[dir="ltr"]`, or set `letter-spacing: normal` under `[dir="rtl"]`. No exceptions.

Related: `text-transform: uppercase` has no effect on Arabic (the script has no case). Harmless, but scope it to Latin for clarity.

### 4.5 Weight

| Role | Latin | Arabic |
|---|---|---|
| Display / `h1` | Cormorant 700 | El Messiri 700 |
| Section title | Cormorant 600 | El Messiri 600 |
| Card title | Cormorant 600 | El Messiri 600 |
| Body | Jost 400 | Tajawal 400 |
| Body light (lead) | Jost 300 | Tajawal 300 |
| UI / buttons | Jost 500 | Tajawal 500 |
| Labels | Jost 600 | Tajawal 700 |

*Tajawal 300 is noticeably lighter than Jost 300 on dark backgrounds — prefer 400 for Arabic body copy at small sizes.*

---

## 5. Spacing

A 4px base unit. This replaces the current ad-hoc values (`26px`, `22px`, `44px`, `56px`, `38px`, `34px`, `30px`) which share no rhythm.

| Token | Value | Typical use |
|---|---|---|
| `--space-3xs` | 4px | Icon-to-label |
| `--space-2xs` | 8px | Tight internal padding |
| `--space-xs` | 12px | Chip and tag padding |
| `--space-sm` | 16px | Grid gaps, field spacing |
| `--space-md` | 24px | Card padding, stack gaps |
| `--space-lg` | 32px | Card padding (large), group separation |
| `--space-xl` | 48px | Between content blocks |
| `--space-2xl` | 64px | Section header to content |
| `--space-3xl` | 96px | Between major sections |
| `--space-4xl` | 128px | Hero breathing room |

**Section rhythm:** `--section-py: clamp(64px, 9vw, 112px)` *(keeps the existing feel, now named)*

**Rules:**
1. Every margin, padding and gap uses a token. No raw pixel values.
2. Vertical rhythm inside a block comes from **one** gap value, not individually tuned margins.
3. Spacing scales with the section, not with the element — a card's internal padding stays constant across breakpoints; the space *around* it grows.

---

## 6. Shape, elevation, borders

| Token | Value | Use |
|---|---|---|
| `--radius-sm` | 10px | Form fields, small chips |
| `--radius-md` | 14px | Cards, images *(existing `--radius`)* |
| `--radius-lg` | 20px | Feature panels |
| `--radius-pill` | 999px | Buttons, filters, language switch |

| Token | Value | Use |
|---|---|---|
| `--shadow-sm` | `0 4px 12px rgba(0,0,0,.25)` | Subtle lift |
| `--shadow-md` | `0 12px 30px rgba(0,0,0,.3)` | Cards on hover |
| `--shadow-lg` | `0 20px 50px rgba(0,0,0,.35)` | Modals, feature images *(existing `--shadow`)* |
| `--shadow-gold` | `0 12px 30px rgba(201,162,75,.3)` | Gold buttons only |

---

## 7. Focus — a designed state, not an afterthought

The site currently has **no focus indicator anywhere**, and form fields explicitly remove theirs. This is the single most impactful visual addition in the system.

```css
--focus-ring-color: #e3c879;   /* 11.85:1 against the page background */
--focus-ring-width: 2px;
--focus-ring-offset: 3px;
```

```css
:where(a, button, input, select, textarea, [tabindex]):focus-visible {
  outline: var(--focus-ring-width) solid var(--focus-ring-color);
  outline-offset: var(--focus-ring-offset);
  border-radius: var(--radius-sm);
}
```

**Why `:focus-visible` rather than `:focus`:** the ring appears for keyboard users and stays hidden for mouse clicks — accessibility without visual cost.

Requirement is 3:1 for non-text indicators. Gold delivers **11.85:1**, and it looks deliberate on a black-and-gold site. This is the rare accessibility fix that improves the design.

---

## 8. Motion

| Token | Value | Use |
|---|---|---|
| `--dur-fast` | 160ms | Colour, opacity |
| `--dur-base` | 280ms | Transforms, hovers |
| `--dur-slow` | 480ms | Panels, menus |
| `--dur-reveal` | 700ms | Scroll reveals |
| `--ease-out` | `cubic-bezier(.22, 1, .36, 1)` | Default *(existing `--ease`)* |
| `--ease-in-out` | `cubic-bezier(.65, 0, .35, 1)` | Two-way transitions |

**Reduced motion — required, and currently almost entirely missing.** Only one of five animations respects the preference today; the infinite full-screen hero zoom does not.

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: .01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: .01ms !important;
    scroll-behavior: auto !important;
  }
}
```

Scroll-revealed content must remain **visible** under reduced motion — never left at `opacity: 0`.

---

## 9. Components

### Buttons

| Variant | Fill | Text | Use |
|---|---|---|---|
| **Primary** | Gold gradient 135° | `#0d0d0d` | One per section. Booking actions |
| **Secondary** | Transparent, 1px `--text-primary` border | `--text-primary` | Alongside primary |
| **Tertiary** | Transparent, 1px `--gold` border | `--gold-light` | Low-emphasis |

- Padding `14px 32px` · `--radius-pill` · `--text-base` · weight 500
- **Minimum target 44×44px**, including the language switcher (currently 27×29px)
- Hover: `translateY(-2px)` + shadow. Active: no lift, `--gold-deep`
- Focus: the ring from §7

### Cards

`--ink-700` background · `--border-subtle` · `--radius-md` · `--space-lg` padding · hover raises 6px, border → `--gold`, `--shadow-md`.

### Form fields

`--ink-800` background · `--border-subtle` · `--radius-sm` · `14px 16px` padding · `--text-base`.
Label `--text-sm` / `--text-muted`. Placeholder `--text-subtle` (**not** the browser default, which fails contrast).
Focus: ring from §7 **plus** border → `--gold`. Error: border `#e5837a` + message text, never colour alone.

### Section header

```
eyebrow    --text-sm · --gold · --tracking-label (Latin only) · uppercase (Latin only)
    ↓ --space-xs
title      --text-2xl · --text-primary · --leading-heading
    ↓ --space-sm
intro      --text-md · --text-secondary · max-width 60ch
    ↓ --space-2xl
content
```

---

## 10. Trilingual rules

The part most easily got wrong, and where the current site has real defects.

| Rule | Why |
|---|---|
| **Never letter-space Arabic** | Breaks cursive joining. See §4.4. Currently violated site-wide |
| **Arabic gets more line height** | Diacritics above, deeper descenders below |
| **Use logical properties** — `inset-inline-start`, `margin-inline`, `padding-inline` | Layout mirrors automatically. Already done well, except the gallery zoom icon (`left: 16px`) which stays stuck on the left in Arabic |
| **Numbers and phone numbers stay LTR** inside RTL text — mark with `dir="ltr"` | Already done correctly |
| **Mirror directional icons** (arrows, chevrons); **never mirror** logos, photographs or clocks | |
| **`aria-label` and `alt` must be translated** | Currently hardcoded Swedish across all three languages |
| **Latin brand name stays Latin in Arabic text** | "ELEGANTMEDIA" is a wordmark, not a word to transliterate |
| **Test at the longest translation** | Swedish compounds ("Bröllopsfotografering", "Flygfoto med drönare") are the layout stress case |

---

## 11. Logo

There is no logo file. The wordmark is currently rendered as CSS text — `ELEGANT` above `MEDIA`, gradient-filled from white through gold.

**Keep the construction** (it's sharp at every size and needs no image), and formalise it:

- Cormorant Garamond 700 · `--tracking-logo` · gradient `#f5f4f1 → #e3c879 → #a8842f` at 135°
- `MEDIA` at 0.4em, `--tracking-label`, centred beneath
- **Always Latin**, in all three languages
- Needs a solid-gold fallback for contexts where gradient text fails — email signatures, social avatars, favicons

**Recommended next step (outside this pass):** a proper SVG logo file. The CSS wordmark can't be used on a business card, an invoice, a watermark, or an Instagram profile — all of which this business needs. It's a small, cheap, high-value job for a designer.

---

## 12. Applying this

Order matters — tokens must land before anything consumes them:

1. **Tokens first** — colour, type, spacing, radius, motion added to `:root`. Nothing looks different yet.
2. **Semantic colours** — replace `#ddd`/`#ccc`/`#cfcfcf`/`#666`. Fixes the one contrast failure.
3. **Type scale** — replace the eleven one-off sizes.
4. **Spacing scale** — replace ad-hoc values.
5. **Focus states** — §7. Biggest single visible improvement.
6. **Script rules** — §4.4 and §10. Fixes Arabic typography.
7. **Components** — buttons, cards, fields aligned to spec.
8. **Delete dead CSS** — `.hero-eyebrow`, `.logo-mark`, `.logo-img`, `.g-item.hide`, `.logo-text em`.

Steps 1–2 are invisible to visitors and safe to ship alone. Steps 3–4 shift the layout subtly and need a proper look on the preview. Step 5 is the one people will notice and like.

---

## 13. Quick reference

```css
:root {
  /* Surfaces */
  --ink-900:#0d0d0d; --ink-800:#161616; --ink-700:#1c1c1c;
  /* Accent */
  --gold:#c9a24b; --gold-light:#e3c879; --gold-deep:#a8842f;
  /* Text */
  --text-primary:#f5f4f1; --text-secondary:#c4c2bd; --text-muted:#9a9a9a;
  --text-subtle:#8f8d88; --text-accent:#e3c879; --text-on-gold:#0d0d0d;
  --text-error:#e5837a;
  /* Borders */
  --border-subtle:rgba(255,255,255,.07); --border-default:rgba(201,162,75,.25);
  --surface-gold-wash:rgba(201,162,75,.12);
  /* Type */
  --text-xs:.75rem; --text-sm:.875rem; --text-base:1rem; --text-md:1.125rem; --text-lg:1.3125rem;
  --text-xl:clamp(1.5rem,1.1rem + 1.6vw,1.75rem);
  --text-2xl:clamp(1.875rem,1.3rem + 2.4vw,2.5rem);
  --text-3xl:clamp(2.5rem,1.4rem + 5vw,4.25rem);
  --leading-display:1.08; --leading-heading:1.25; --leading-body:1.65;
  --leading-display-ar:1.3; --leading-heading-ar:1.5; --leading-body-ar:1.95;
  --tracking-display:-.015em; --tracking-label:.14em; --tracking-logo:.22em;
  /* Space */
  --space-3xs:4px; --space-2xs:8px; --space-xs:12px; --space-sm:16px; --space-md:24px;
  --space-lg:32px; --space-xl:48px; --space-2xl:64px; --space-3xl:96px; --space-4xl:128px;
  --section-py:clamp(64px,9vw,112px);
  /* Shape */
  --radius-sm:10px; --radius-md:14px; --radius-lg:20px; --radius-pill:999px;
  --shadow-sm:0 4px 12px rgba(0,0,0,.25);
  --shadow-md:0 12px 30px rgba(0,0,0,.3);
  --shadow-lg:0 20px 50px rgba(0,0,0,.35);
  --shadow-gold:0 12px 30px rgba(201,162,75,.3);
  /* Focus */
  --focus-ring-color:#e3c879; --focus-ring-width:2px; --focus-ring-offset:3px;
  /* Motion */
  --dur-fast:160ms; --dur-base:280ms; --dur-slow:480ms; --dur-reveal:700ms;
  --ease-out:cubic-bezier(.22,1,.36,1); --ease-in-out:cubic-bezier(.65,0,.35,1);
}
```

---

## 14. Deviations from the original spec

Recorded so the document matches the code rather than the first draft of the
intention.

| Spec said | Built instead | Why |
|---|---|---|
| Rename surfaces to `--ink-900/800/700` | Kept `--black`, `--black-soft`, `--black-card` | Renaming touches every rule in the file for no functional gain. The existing names are clear and already applied consistently. |
| Type scale topping out at 40px / 68px | `2.9rem` / `4.8rem` — the sizes already in use | The drafted scale would have shrunk every section heading and the hero. The old sizes were well judged; they were just arbitrary. The scale now describes them. |
| Snap every spacing value to the scale | Tokens defined; applied where a value already sat on or within 2px of a step | Mechanically snapping `22→24`, `38→32`, `56→64` across a working layout shifts the design for the sake of tidiness and risks regressions no one asked for. The scale is the standard for new work; deliberately tuned component internals (button padding, icon boxes) were left alone. |
| `--text-error: #e5837a` | Defined in this document, not yet in the stylesheet | Nothing uses it until the booking form gets real validation in Batch 2. |

Items from this spec **not yet applied**, and where they land:

- **§9 component specs** (button padding, card padding on the scale) — partly
  applied; the remaining tuning is cosmetic and can follow.
- **§5 spacing** — see above.
- **§11 logo** — still the CSS wordmark. A real SVG logo file is the
  recommended next step and sits outside this pass.
- **Emoji icons (§L6 in the audit)** — Batch 6b.

---

**This spec is deliberately conservative.** It keeps the palette, the fonts and the character that already exist, and supplies the rhythm, states and script rules that don't. The site shouldn't look like a different brand afterwards — it should look like the same brand, done properly.
