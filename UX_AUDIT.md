# Elegant Media — Full Audit

**Date:** 2026-09-14
**Audited:** https://namarsandil.github.io/Elegant-Media/ (live) + local source (verified byte-identical)
**Scope:** UX/IA, visual design & brand, accessibility (WCAG 2.2 AA), performance, responsive, SEO, bugs
**Status:** Audit only. **No fixes applied. No files changed.**

Every finding below was verified against the running site — contrast ratios computed from live colours, keyboard behaviour tested in the browser, network and font payloads measured, not inferred from reading code.

---

## Summary

**51 findings.**

| Severity | Count | What it means |
|---|---|---|
| 🔴 **Critical** | 5 | Losing money or actively harming the business right now |
| 🟠 **High** | 18 | Credibility, legal, or blocks real users from using the site |
| 🟡 **Medium** | 17 | Meaningful quality, performance and reach problems |
| ⚪ **Low** | 11 | Tidy-ups and polish |

| Category | Findings |
|---|---|
| Bugs & broken states | 8 |
| Content credibility & brand consistency | 11 |
| Accessibility | 14 |
| SEO | 9 |
| Performance | 6 |
| UX & information architecture | 7 |
| Responsive/mobile | 3 |
| Visual design system | 4 |

*(Some findings sit in two categories; the total row counts each finding once.)*

### The three sentences that matter

1. **The booking form has silently deleted every enquiry ever submitted.** Not "sends to the wrong place" — it shows a tick, wipes the form, and throws the data away.
2. **One tag in the page source tells Google the real website is `example.com`.** As long as it's there, the site is competing against itself for its own name.
3. **The site cannot be used without a mouse.** The entire 12-image portfolio is unreachable by keyboard, and tabbing on mobile sends focus into an invisible menu off the side of the screen.

---

## 🔴 Critical

### C1 — The booking form throws every enquiry away
**Category:** Bugs · **Where:** `script.js:160–167`, `index.html:361–392`

```js
document.getElementById('bookingForm').addEventListener('submit', e => {
  e.preventDefault();          // stop the browser sending it
  note.hidden = false;         // show "✓ we received your request"
  e.target.reset();            // wipe the fields
});
```

The form has no `action`, no `method`, and no backend. `e.preventDefault()` cancels the submission and nothing replaces it. The customer sees *"✓ Din förfrågan har mottagits! Vi kontaktar dig snart."* ("Your request has been received! We'll contact you soon.") and believes they've booked.

**Impact:** Every name, phone number, event type and date submitted since launch is gone. The customer thinks they're waiting for a reply that will never come. This is worse than having no form — it converts an interested lead into someone who thinks they were ignored.

**Verified:** Form element has `action: null, method: null`, no Netlify/Formspree/Getform attributes.

---

### C2 — Canonical tag points to `example.com`
**Category:** SEO · **Where:** `index.html:14`

```html
<link rel="canonical" href="https://example.com/" />
```

A canonical tag tells Google "the real version of this page lives here." This one points at a placeholder domain that isn't the business's.

**Impact:** Google is instructed to credit `example.com` with this page's content and to treat the real site as a duplicate. This suppresses ranking for the business's own name. It's one line, and it's the single highest-leverage SEO fix on the site.

---

### C3 — Fabricated review ratings in structured data
**Category:** SEO / Legal · **Where:** `index.html:31–45`

```json
"aggregateRating": { "ratingValue": "5", "reviewCount": "127" }
```

Now confirmed by the owner as invented. This is machine-readable markup submitted to Google claiming 127 five-star reviews that don't exist.

**Impact:** Google's structured data policy explicitly prohibits review markup that doesn't correspond to genuine reviews. Penalties range from losing rich-result eligibility to a manual action against the whole site. Separately, in Sweden, publishing invented customer ratings is a marketing-law problem (*marknadsföringslagen* — misleading commercial claims). This must be removed, not adjusted.

---

### C4 — The entire portfolio is unreachable by keyboard
**Category:** Accessibility (WCAG 2.1.1 Keyboard, Level A) · **Where:** `script.js:34–43`

Gallery items are built as `<div>` elements with a click handler, no `tabindex`, no `role`, and no keyboard handler:

```js
const div = document.createElement('div');
div.className = 'g-item reveal visible';
div.addEventListener('click', () => openLightbox(idx));
```

**Verified live:** `tag: "DIV", tabindex: null, role: null`.

**Impact:** Anyone using a keyboard instead of a mouse — motor-impairment users, screen reader users, and people who simply navigate by Tab — cannot open a single one of the 12 portfolio images. For a photography business, the portfolio *is* the product. This is a Level A failure, the most basic tier of the standard.

---

### C5 — Mobile menu stays in the keyboard tab order while closed
**Category:** Accessibility (WCAG 2.4.3 Focus Order, 2.4.7 Focus Visible) · **Where:** `styles.css:88–94`

The mobile menu is hidden by sliding it sideways (`transform: translateX(100%)`) but remains `visibility: visible` and `display: flex`.

**Verified live at 375px:** the closed menu's first link is focusable and sits at `x: 504px` — 129px beyond the right edge of a 375px screen.

**Impact:** A keyboard user on mobile tabs off the logo and then presses Tab seven times through links they cannot see, with no visible focus indicator anywhere on screen. Focus effectively vanishes. This is the classic off-canvas menu bug and it makes the whole page confusing to navigate.

---

## 🟠 High

### Content credibility

### H1 — Three of the four headline statistics are invented
**Category:** Content credibility · **Where:** `index.html:99–106`

`850 weddings documented`, `127 five-star reviews`, `40 local awards`. Owner has confirmed these are fabricated; only **12 years of experience** is accurate.

**Impact:** These sit directly under the hero as the first proof point a visitor sees. Beyond the honesty problem, "850 weddings" is internally inconsistent with a business whose portfolio is stock photography and whose Instagram is a new account — the claim invites the exact scrutiny it's trying to prevent.

### H2 — All three testimonials are invented, with fake faces
**Category:** Content credibility · **Where:** `index.html:300–320`, `i18n.js` (`tst1`–`tst3`)

"Emma & Erik", "Anna Lindqvist", "Johan Andersson" — invented names with portraits pulled from `i.pravatar.cc`, a fake-avatar generator.

**Impact:** Same legal exposure as C3 under Swedish marketing law. Also a practical risk: reverse image search on those avatars immediately exposes them.

### H3 — The portfolio is stock photography
**Category:** Content credibility · **Where:** `script.js:2–15`, `index.html:277`

All 13 photographs are Unsplash stock. **Per your decision, these stay as placeholders until the client supplies real photos** — that's a reasonable call and the Unsplash licence permits the use.

Two things still need doing, and they're what this finding tracks:
- The site currently presents stock images as *"Bläddra bland ett utvalt urval av vårt arbete"* ("Browse a curated selection of our work"). Copy that claims authorship of someone else's photos is the part that's a problem, not the placeholders themselves.
- The image plumbing should be rebuilt now (local `assets/`, fixed aspect ratios, `srcset`) so that swapping in real photos later is a drag-and-drop job, not a rebuild.

### H4 — Four different phone numbers across the site
**Category:** Bugs / Brand · **Where:** `index.html:38, 331, 335, 189`

| Location | Number |
|---|---|
| Structured data (`index.html:38`) | `+467200281` |
| Contact — displayed & `tel:` link (`:331`) | `+46 7200281` |
| Contact — WhatsApp (`:335`) | `+46 76 200 281` |
| Web Design button (`:189`) | **`+46 73 561 7258`** |

`+467200281` has 9 digits after the country code; Swedish mobile numbers have 9 digits starting `7` after the `+46` — this one is missing a digit and **will not connect**. The Web Design button goes to an entirely different person or line.

**Impact:** The primary "call us" link on the site is broken. A visitor who taps it gets a failed call, not a booking.

### H5 — Email address belongs to a different business
**Category:** Brand · **Where:** `index.html:339`

`info@lumsa.com` — a leftover from the template this site was adapted from. Almost certainly not a mailbox anyone at Elegant Media monitors.

**Impact:** Combined with C1 (broken form), this means **two of the three contact routes on the site go nowhere.** Only WhatsApp works.

### H6 — Social links point at three different brands
**Category:** Brand · **Where:** `index.html:344–356`

| Icon | Points to | On brand? |
|---|---|---|
| WhatsApp | `+46 76 200 281` | ✅ |
| Instagram | `@elegant.foto.video` | ✅ |
| TikTok | `@chukrifreedom` | ❌ personal account |
| YouTube | `@ArtNovaMEDIA` | ❌ different brand |

**Impact:** A visitor clicking through to "see more work" lands on an unrelated account. It reads as either careless or as a business that has changed names repeatedly.

### H7 — Business name misspelled in the Google listing data
**Category:** SEO / Brand · **Where:** `index.html:35`

`"name": "ELEGANTEDIA"` — missing the M. This is the name Google reads for the business listing.

### H8 — A different studio's name is in the page source
**Category:** Brand · **Where:** `index.html:279`

The static HTML About paragraph reads *"في استوديو **لُمسة ضوء**"* ("at **Lumsa** studio"). Once JavaScript runs it's replaced with ELEGANTMEDIA — so most visitors never see it — but it is what's in the raw source that crawlers fetch, what a no-JS visitor sees, and what shows in the split second before the script executes.

*(Precision note: the `i18n.js` translations correctly say ELEGANTMEDIA in all three languages. This leftover exists only in the hardcoded HTML fallback.)*

### H9 — No privacy policy, on a form collecting personal data, in the EU
**Category:** Legal / Trust · **Where:** site-wide

The booking form collects name, phone number, event type, date and free-text details. There is no privacy policy, no consent checkbox, no statement of what happens to the data, and no company identification (org. number, registered address) anywhere on the site.

**Impact:** Under GDPR, collecting personal data requires informing the person what you'll do with it and on what legal basis. For a Swedish business this is also an *e-handelslagen* identification requirement. Right now the form is non-compliant — although, given C1, it isn't actually storing anything, which is a strange kind of accidental safety. Both need fixing together: the moment the form starts working, the compliance gap becomes real.

### Accessibility

### H10 — No visible focus indicator anywhere; form fields actively remove theirs
**Category:** Accessibility (WCAG 2.4.7 Focus Visible, AA) · **Where:** `styles.css:332`

**Verified live:** the entire stylesheet contains exactly one `:focus` rule, and it is this —

```css
.field input:focus, .field select:focus, .field textarea:focus { outline: none; border-color: var(--gold); }
```

Across 46 focusable elements there are no custom focus styles at all. Form fields have their outline explicitly removed and replaced only by a border-colour change — a 1px colour shift that is easy to miss and fails on its own as a focus indicator.

**Impact:** A keyboard user cannot tell where they are on the page. Combined with C5, navigating this site without a mouse is guesswork.

### H11 — Lightbox is not an accessible dialog
**Category:** Accessibility (WCAG 2.4.3, 4.1.2) · **Where:** `index.html:417–424`, `script.js:60–78`

**Verified live**, opening a gallery image:
- No `role="dialog"`, no `aria-modal="true"` → screen readers don't announce it as a dialog
- Focus is **not moved into** the lightbox when it opens
- Background content **remains focusable** while it's open — no focus trap; I tabbed to the nav CTA behind the overlay
- Focus is **not restored** to the triggering image on close — it was left on the background element

Escape and arrow keys do work, and arrow direction correctly flips in RTL — that part was done thoughtfully.

### H12 — No `<main>` landmark and no skip link
**Category:** Accessibility (WCAG 1.3.1, 2.4.1) · **Where:** `index.html`

**Verified:** `main: 0`, no skip link present. Screen reader users have no "jump to content" shortcut, and keyboard users must tab through the full header on every navigation.

### H13 — Heading hierarchy skips a level
**Category:** Accessibility (WCAG 1.3.1) · **Where:** `script.js:38`

The live heading outline runs `h1` → `h2` (Gallery) → **`h4`** (each gallery item). There is no `h3` between them. Screen reader users navigating by heading hit a gap and may assume content is missing.

### H14 — Interface labels stay Swedish in all three languages
**Category:** Accessibility / i18n · **Where:** `index.html:79, 95, 418–421`

**Verified live** by switching to Arabic — these `aria-label` values never change:

| Element | Label (in every language) | Should be |
|---|---|---|
| Menu button | `Meny` | translated |
| Lightbox close | `Stäng` | translated |
| Lightbox previous | `Föregående` | translated |
| Lightbox next | `Nästa` | translated |
| Scroll indicator | `scroll` | meaningful, translated |

Also: the About image's alt text is **hardcoded Arabic** (`فريق التصوير أثناء العمل`) and stays Arabic for Swedish and English visitors.

**Impact:** An Arabic-speaking screen reader user hears Swedish words announced by an Arabic voice synthesiser. The `scroll` label is meaningless in any language.

### SEO & discoverability

### H15 — Page title and description never change with language
**Category:** SEO · **Where:** `script.js:98–120`

**Verified:** switching to Arabic leaves `document.title` as the Swedish string and `og:locale` as `sv_SE` while `<html lang>` becomes `ar`.

**Impact:** Google indexes one Swedish title for a trilingual site. The Arabic and English versions are invisible in search — a third of the content reach, wasted.

### H16 — No `hreflang`, so the three languages don't exist to search engines
**Category:** SEO · **Where:** `index.html` head

**Verified:** zero `hreflang` tags. Because the languages are switched by JavaScript with no distinct URLs, there is nothing for Google to index per language.

**Impact:** The trilingual capability — genuinely one of the site's strengths, and a real differentiator in the Swedish market — delivers **zero** SEO value today. Someone searching in Arabic for a wedding photographer in Sweden will never find this site.

### H17 — Hero image is invisible to the browser's preloader
**Category:** Performance · **Where:** `styles.css:132–135`

The hero background — a **215 KB** image and almost certainly the Largest Contentful Paint element — is set as a CSS `background-image`, not an `<img>`, and has no `<link rel="preload">`.

**Impact:** The browser can't discover it until it has downloaded and parsed the stylesheet, which delays the largest thing on screen. This is one of the most common LCP problems and one of the easiest to fix.

### H18 — No image has width or height
**Category:** Performance (CLS) · **Where:** `index.html`, `script.js:37`

**Verified:** `17/17` images have no `width`/`height` attributes and no `aspect-ratio` on the `<img>` itself.

**Impact:** The browser can't reserve space before images arrive, so content jumps as they load. I measured CLS as 0, but that was on a warm cache and understates real-world behaviour — a first-time visitor on a slow connection will see shifting. Gallery items are partly protected by `aspect-ratio` on their container; the About image and testimonial avatars are not.

---

## 🟡 Medium

### M1 — Half the font payload is never used
**Category:** Performance · **Where:** `index.html:27`

**Verified:** 21 font faces load across 4 families — Cormorant Garamond, El Messiri, Jost, Tajawal. But only two families are ever displayed at once: Jost + Cormorant for Swedish/English, Tajawal + El Messiri for Arabic.

**Impact:** Every Swedish visitor downloads 11 Arabic font faces they will never see, and vice versa. `display=swap` is correctly set, so text stays visible while loading — that part was done right — but the bandwidth is wasted on every single visit.

### M2 — Images are served at one size to every device
**Category:** Performance · **Where:** `script.js:2–15`, `index.html:277`

**Verified:** zero `srcset` attributes, zero modern formats (no WebP/AVIF). Gallery images are fixed at `w=800`, the hero at `w=1920`.

**Impact:** A phone on mobile data downloads the same 1920px hero as a desktop. Unsplash can serve WebP and arbitrary widths via URL parameters, so this is cheap to fix even before real photos arrive.

### M3 — 1.5 MB of images from a third-party server
**Category:** Performance / Risk · **Where:** `script.js`, `index.html`

**Measured live:** 1,506 KB across 13 requests to `images.unsplash.com`, out of ~1,523 KB total page weight. **99% of this website's weight comes from a domain the business doesn't control.**

**Impact:** Performance aside, if Unsplash changes a URL, rate-limits, or goes down, the portfolio disappears. Hosting images in the repo removes the dependency entirely.

### M4 — Language switcher buttons are too small to tap reliably
**Category:** Responsive / Accessibility (WCAG 2.5.8) · **Where:** `styles.css:113–118`

**Measured at 375px:** AR/EN/SV buttons render at **27×29px**. WCAG 2.2 AA requires a 24×24 minimum (these scrape past), but the practical guidance from both Apple and Google is 44×44.

**Impact:** This is the control that makes the site trilingual — arguably its best feature — and it's the hardest thing on the page to tap. Contact links (23px tall) and footer links (29px) are also below comfortable size.

### M5 — Hero uses `100vh`, which overflows on mobile browsers
**Category:** Responsive · **Where:** `styles.css:131`

`min-height: 100vh` measured as `812px` at a 375×812 viewport. On iOS Safari and Android Chrome, `100vh` means the viewport *without* the browser toolbars, so the hero is taller than the visible area and the call-to-action buttons can sit below the fold on first paint. `100svh` (with a `100vh` fallback) is the modern fix.

### M6 — Reduced-motion preference is almost entirely ignored
**Category:** Accessibility (WCAG 2.3.3) · **Where:** `styles.css:257`

**Verified:** exactly one `prefers-reduced-motion` rule exists, covering the floating phone mockup. Not covered:
- `heroZoom` — an **infinite** 18-second zoom on the hero background
- `scrollDot` — an **infinite** 1.6-second bounce
- 44 scroll-reveal transitions
- The animated statistic counters

**Impact:** Visitors who have asked their operating system to reduce motion — commonly people with vestibular disorders, who get genuinely nauseated by it — still get a permanently zooming full-screen background.

### M7 — Toggle controls don't announce their state
**Category:** Accessibility (WCAG 4.1.2) · **Where:** `index.html:69–80`, `script.js:47–52`

**Verified:** filter buttons have no `aria-pressed`; language buttons have no `aria-pressed` and no `lang` attribute; the menu button has no `aria-expanded` and no `aria-controls`.

**Impact:** A screen reader user can't tell which gallery filter is active, which language is selected, or whether the menu is open. Active state is conveyed by colour alone.

### M8 — Copyright text fails contrast
**Category:** Accessibility (WCAG 1.4.3 AA) · **Where:** `styles.css:344`

**Computed:** `#666` on `#161616` = **3.15:1**. AA requires 4.5:1 for text this size. Lightening to roughly `#8a8a8a` clears it.

*(Credit where due: I checked 11 other colour pairings and every one passes, several comfortably — `--grey` on black is 6.91:1, gold on black 8.10:1. The palette is fundamentally sound. This is the one miss.)*

### M9 — Form placeholder text fails contrast
**Category:** Accessibility (WCAG 1.4.3 AA) · **Where:** browser default on `styles.css:327`

**Measured:** the browser's default placeholder renders as `rgb(117,117,117)` on the `#161616` field background ≈ **4.1:1**. Just under the 4.5:1 threshold.

### M10 — Success message is silent to screen readers
**Category:** Accessibility (WCAG 4.1.3) · **Where:** `index.html:391`, `script.js:162`

The confirmation note is revealed by removing `hidden`, with no `aria-live` region. **Verified:** `aria-live: null`. A screen reader user submits the form and receives no feedback at all. (Currently moot — see C1 — but it must be fixed alongside it.)

### M11 — The date field accepts dates in the past
**Category:** Bugs · **Where:** `index.html:385`

**Verified:** no `min` attribute. Someone can request a wedding shoot for last March.

### M12 — Phone placeholder shows a Gulf number format
**Category:** Bugs / Localisation · **Where:** `index.html:375`

The placeholder is `05xxxxxxxx` — a Saudi/Gulf mobile format. Swedish mobiles start `07`. Another template leftover, and one that will actively mislead Swedish customers into second-guessing their own number.

### M13 — The Web Design section doesn't belong on this page
**Category:** UX / IA · **Where:** `index.html:172–232`

A full section pitching website design services sits between Services and About, with its own CTA to a different phone number.

**Impact:** The page's job is to convert someone planning a wedding into an enquiry. A mid-page pitch for an unrelated service interrupts that, dilutes the positioning ("are they photographers or a web agency?"), and adds a competing call-to-action at the exact point the visitor should be moving toward booking. It's also the only section whose CTA leaves the page entirely. Worth keeping as a separate page, not here.

*(Noted: the hand-built SVG mockup in this section is genuinely good work — clean, on-palette, fully scalable. If the section moves, the asset is worth keeping.)*

### M14 — No pricing or package information anywhere
**Category:** UX / IA · **Where:** site-wide

The site asks for a booking without giving any indication of cost — not even "packages from X kr". The structured data says `priceRange: "$$$"`, which is the only price signal and it's invisible to visitors.

**Impact:** Price is the single most common question for wedding photography, and its absence is the most common reason people bounce to a competitor who publishes it. Even three tiers with "from" prices would cut unqualified enquiries and raise qualified ones.

### M15 — Nothing addresses the christening/family-event audience
**Category:** UX / IA / Content · **Where:** `i18n.js`, Services section

**Verified:** zero mentions of *dop* (christening), *kyrka* (church) or equivalent in any of the three languages. The stated audience explicitly includes families planning christenings and church events; the closest the site comes is "birthdays, graduations, family evenings."

**Impact:** A parent searching *dopfotograf* finds nothing here, and a visitor who lands anyway sees a site that reads as wedding-only.

### M16 — No sitemap.xml and no robots.txt
**Category:** SEO · **Where:** repo root — **verified both return 404**

Low effort, and both help crawlers index the site properly.

### M17 — Business schema is missing everything that makes it useful
**Category:** SEO · **Where:** `index.html:31–45`

Beyond the fake rating (C3) and the name typo (H7), the `LocalBusiness` block has **no address, no geo coordinates, no opening hours, no URL, and no `sameAs` social links**. A local business listing with no location can't rank for "wedding photographer near me" — the query that matters most.

The type should also be `PhotographyBusiness`, a schema.org subtype that exists specifically for this.

---

## ⚪ Low

| # | Finding | Where |
|---|---|---|
| **L1** | **Dead CSS** — `.hero-eyebrow`, `.logo-mark`, `.logo-img`, `.g-item.hide`, `.logo-text em` are all styled but **verified absent from the DOM** | `styles.css:135, 77–78, 213` |
| **L2** | **`.g-item.tall` does nothing below 768px** — verified both tall and normal items compute to `4/3` at mobile, so the `tall: true` flag in the gallery data has no effect on phones | `styles.css:186–187` |
| **L3** | **Gallery reveal animation never runs** — items are created with `'g-item reveal visible'`, so the `.reveal` class is applied and immediately cancelled. Dead code path | `script.js:36` |
| **L4** | **Zoom icon doesn't flip in RTL** — `.g-zoom` uses physical `left: 16px` instead of `inset-inline-start`, verified computing to `left: 16px / right: 287px` in Arabic mode. Everything else in the stylesheet uses logical properties correctly | `styles.css:205` |
| **L5** | **Star ratings are literal `★★★★★` text** — screen readers announce "black star, black star, black star…" | `index.html:302` |
| **L6** | **Service icons are emoji** (💍 💐 🎉 🎬 🚁) — these render as different pictures on Windows, Mac, Android and iOS, so the "luxury" section looks different to every visitor and can't be brand-controlled | `index.html:137–163` |
| **L7** | **Swedish grammar error** — "Lättanvänt kontrollpanel" should be "Lättanvänd kontrollpanel" (common-gender agreement) | `i18n.js:169` |
| **L8** | **No favicon** — verified 404. Browser tabs and bookmarks show a blank page icon | repo root |
| **L9** | **No 404 page** — a mistyped URL gets GitHub's generic error page, not the brand | repo root |
| **L10** | **`<meta name="keywords">` is obsolete** — ignored by every major search engine since ~2009. Harmless, but it signals dated SEO advice | `index.html:11` |
| **L11** | **Scripts aren't deferred** — `i18n.js` and `script.js` load without `defer`. They're at the end of `<body>` so they don't block rendering, but they do delay `DOMContentLoaded` | `index.html:426–427` |

---

## Design system assessment

Worth stating plainly, because it changes what Phase 2 should do: **the existing design system is better than the brief suggested, and should be extended rather than replaced.**

**What's already working:**
- A proper token layer in `:root` — 9 colours plus radius, shadow, easing and font tokens
- **Zero hardcoded hex colours outside `:root`** in 22 KB of CSS. Verified. That's real discipline and it's rare
- A coherent black-and-gold palette that passes contrast almost everywhere (11 of 12 pairings checked)
- Sensible component classes (`.btn` with four variants, `.container`, `.section`, `.section-head`)
- Logical CSS properties (`inset-inline-start`, `margin-inline`) used consistently for RTL — with one exception (L4)
- Mobile-first, 5 clean breakpoints, CSS Grid throughout, no horizontal overflow at 375px

**What's missing, and what BRAND.md will need to specify:**

| Gap | Evidence |
|---|---|
| **No spacing scale** | Padding and gap values are ad-hoc one-offs — `26px`, `22px`, `44px`, `56px`, `38px`, `34px` with no underlying rhythm |
| **No type scale** | Font sizes are individually tuned `clamp()` calls and arbitrary rems — `1.02rem`, `1.05rem`, `.92rem`, `.95rem`, `1.1rem`, `1.15rem` — that don't form a scale |
| **No defined states** | No focus, no disabled, no loading, no error states defined anywhere |
| **Off-token greys leaking in** | `#ddd`, `#ccc`, `#cfcfcf`, `#666` appear directly in rules instead of as tokens — the one crack in otherwise good token discipline, and the source of M8 |
| **No documentation** | The system exists only as CSS; nothing states the rules |

---

## What I checked that was fine

Recording these so the next pass doesn't re-audit them:

- ✅ **Colour contrast** — 11 of 12 pairings pass AA, several well above (white on black 19.4:1, gold-light on card 10.4:1). Only M8 fails
- ✅ **Alt text** — all 17 images have `alt`; the 12 gallery images build meaningful translated alt text from title + category; decorative avatars correctly use `alt=""`. Only the About image is wrong (H14)
- ✅ **Single `<h1>`** — verified exactly one
- ✅ **Translation completeness** — 56 keys in each of Arabic, English and Swedish, no gaps or missing strings
- ✅ **Swedish copy quality** — reads naturally, clearly not machine-translated. One grammar slip (L7)
- ✅ **No horizontal overflow** at 375px — verified `scrollWidth === clientWidth === 375`
- ✅ **No console errors** on load
- ✅ **`font-display: swap`** correctly set on the Google Fonts request
- ✅ **`preconnect`** correctly set for both font hosts
- ✅ **RTL arrow keys** in the lightbox correctly reverse direction for Arabic — a genuinely thoughtful detail
- ✅ **`loading="lazy"`** on all below-the-fold images
- ✅ **`rel="noopener"`** on all external links
- ✅ **Fast server response** — TTFB 132ms, DOM ready 380ms. GitHub Pages is performing well; the weight problem is entirely images and fonts

---

## Measurement caveats

Being precise about what I can and can't claim:

- **LCP and FCP could not be measured reliably** in the preview browser (the performance entries returned empty after reload). H17 is diagnosed from the architecture — a 215 KB CSS background image with no preload — rather than from a measured number. A Lighthouse run on a real device should confirm it before Phase 3.
- **CLS measured 0**, but on a warm cache with images already downloaded. Given 17/17 images lack dimensions (H18), the real first-visit number will be higher. Treat 0 as unrepresentative.
- **Screenshots below the fold return black** in this preview pane. *(Corrected 2026-09-14: I originally attributed this to the blurred navbar. I tested that in Phase 3 by disabling `backdrop-filter`, and it made no difference — nor did disabling all animations and scroll reveals. The actual behaviour is that the pane composites scrolled content at the wrong vertical offset, degrading to fully black at larger scroll positions. Cause still unidentified; likely the `overflow-x: hidden` on `html` combined with fixed positioning.)* It is a capture limitation, not a site defect — all visual findings above were verified through the live DOM and computed styles instead. Phase 4 QA needs real-device screenshots.

---

## Suggested order of attack (detail comes in PLAN.md)

1. **Stop the bleeding** — C1 (form), C2 (canonical), C3 (fake ratings), H4/H5 (contact details). These are small edits with immediate business impact.
2. **Make it usable without a mouse** — C4, C5, H10, H11, H12, H13.
3. **Make it honest** — H1, H2, H3, H6, H7, H8, H9.
4. **Make it findable** — H15, H16, M16, M17.
5. **Make it fast** — H17, H18, M1, M2, M3.
6. **Then design** — the visual system work, once the foundation is sound.

---

**Next:** Phase 2 — turning this into `PLAN.md` (quick wins / foundational fixes / polish, with effort and impact for each) and `BRAND.md` (type scale, spacing system, colour usage). No code will be written until that plan is approved.
