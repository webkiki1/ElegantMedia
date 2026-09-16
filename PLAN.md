# Elegant Media — Implementation Plan

**Date:** 2026-09-14
**Based on:** `UX_AUDIT.md` (51 findings) + owner decisions
**Status:** Awaiting approval. **No code written yet.**

---

## Decisions already locked in

| Decision | Made by | Effect on the plan |
|---|---|---|
| Stats are fake except "12 years" | Owner | The 4-stat bar is replaced, not adjusted (→ F7) |
| No real photos yet — keep placeholders | Owner | Image *plumbing* rebuilt now so real photos drop straight in (→ F9) |
| **No published prices** — every quote is personalised after contact | Owner | No price table. Instead: a process section that removes the uncertainty prices normally answer (→ F8) |
| **Add christenings / church events** | Owner | New service, new gallery category, new keyword target (→ F8) |
| Web Design section doesn't belong here | Owner + me | Moves to its own page, asset preserved (→ F10) |

---

## How we'll work

Good news that changes the plan: **the `gh` command line on this machine is already signed in to GitHub as `NamarSandil`** with full repository permissions. No connector setup needed. Everything below can run today.

### Before any code is touched

1. **Copy the folder** to `Elegant Media BACKUP 2026-09-14` on the Desktop. Takes a second, needs no technical knowledge, and is the panic button.
2. **Connect the local folder to GitHub properly** — it's currently a loose folder with no history.
3. **Tag today's live site as `v0-pre-redesign`** — a permanent bookmark. "Put it back exactly as it was" becomes one command, forever.
4. **Set a git identity** for commit authorship (not currently configured on this machine).

### For every batch after that

```
branch  →  build  →  preview link  →  you review  →  approve  →  merge  →  live
```

- **Nothing touches the live site without your explicit go-ahead.** Work happens on a branch; `main` is what's public.
- **Each batch gets a preview link** you can open on your phone and send to the client. GitHub Pages can't do preview links on its own, so I'll publish each batch as a private preview page — real HTML, real CSS, real behaviour, its own URL, zero setup for you.
- **Each batch gets a pull request** — a page on GitHub showing exactly which lines changed, in red and green. That's the permanent record, and the undo button.
- **Each batch gets a plain-language summary** from me: what changed, why, what to look at.

### If something goes wrong after a merge

```bash
git revert <the merge> && git push
```

Live site back to the previous state in about 30 seconds. No files lost, full history kept.

---

## ⚠️ What I need from you before some of this can ship

I've deliberately structured the batches so **work starts immediately** and these only block specific items. Nothing here holds up Batches 1–2.

| # | What I need | Blocks | Why I can't guess |
|---|---|---|---|
| **N1** | **The correct phone number.** The site has four; the one in the header is missing a digit and won't connect | H4 | Publishing the wrong number is worse than the current state |
| **N2** | **A real email address.** Currently `info@lumsa.com` — a different company | H5, C1 | Same |
| **N3** | **An email to receive booking enquiries at** (can be the same as N2) | C1 | The form needs somewhere to send to |
| **N4** | **TikTok + YouTube:** correct accounts, or permission to remove them | H6 | They currently point at a personal account and an unrelated brand |
| **N5** | **Business address or service area** (e.g. "Stockholm + 100km") | H9, M17 | Local SEO is the highest-value SEO work available, and it needs a location. Also a legal identification requirement for a Swedish business |
| **N6** | **Company registration details** (org.nr) if registered | H9 | Required on Swedish commercial sites |
| **N7** | **Any real testimonials?** Even a WhatsApp message or Instagram comment, with permission to quote | H2 | Otherwise the section comes out entirely until real ones exist |

**If N1–N3 aren't available quickly, say so** — I have a fallback that makes the form work today using the WhatsApp number that already functions (see F1).

---

## Three decisions I need from you

### D1 — How should the booking form actually send?

The form must start working. Three ways, all free:

| Option | How it works | Pros | Cons |
|---|---|---|---|
| **A. Email service** *(recommended)* | Form posts to a free service (Web3Forms) that emails the enquiry to you | Proper enquiry in your inbox; works for everyone; no account signup, just an email address | Depends on a third party |
| **B. WhatsApp handoff** | Submitting opens WhatsApp with the details pre-written | Zero setup, uses the number that already works, no data stored anywhere (simplest privacy story) | Requires the visitor to have WhatsApp; no record if they don't send |
| **C. Both** | Form emails you, plus a prominent "or message us on WhatsApp" button | Catches everyone | Marginally more to build |

**My recommendation: C.** WhatsApp is currently the only contact route on the site that works at all, so it's clearly how this business already operates — but relying on it alone loses anyone who'd rather type an email. Building both is maybe 20 minutes more than building one.

### D2 — Do you want the three languages to actually show up in Google?

This is the biggest architectural question in the plan, so I want it decided consciously rather than by default.

**Today:** the site is trilingual, but Google only ever sees the Swedish version. Arabic and English deliver **zero** search value. Someone searching in Arabic for a wedding photographer in Sweden will never find this site.

| Option | What it means | Effort | My read |
|---|---|---|---|
| **A. Leave it** | Languages stay a nice on-site feature, invisible to search | None | Wastes a genuine competitive advantage |
| **B. Three real pages** — `/` (Swedish), `/en/`, `/ar/` — each with its own translated title, description and language tags, generated automatically from the single translation file | Each language becomes findable in search. Content stays in **one** file so there's no double-maintenance | **L** | **Recommended.** The Arabic-speaking community in Sweden is a real, underserved market for this service, and right now the site is invisible to it |

Option B adds one automated step that rebuilds the three pages whenever you edit text. You would never run it manually — it happens on GitHub. But it *is* the one place this plan adds machinery to a currently machinery-free site, so I'm flagging it rather than slipping it in.

**If you're unsure, pick B but schedule it last** (Batch 6). Everything else lands first, and if it turns out to be more trouble than it's worth we simply stop before it.

### D3 — Testimonials in the meantime?

The fake ones must go (legal exposure — see audit C3/H2). Until real ones exist:

- **A.** Remove the section entirely *(recommended — an absent section reads as "new business", a fake one reads as dishonest)*
- **B.** Replace with a non-testimonial trust section — what's included, how delivery works, equipment
- **C.** Leave a designed empty state inviting the first review

---

# Quick wins

*Small, low-risk, immediately valuable. Roughly a day's work in total.*

| ID | Change | Findings | Effort | Impact |
|---|---|---|---|---|
| **Q1** | Point the canonical tag at the real site instead of `example.com` | C2 | **XS** | 🔴 **Very high** — one line; currently suppressing the site's own search ranking |
| **Q2** | Delete the invented review markup (`5 stars, 127 reviews`) | C3 | **XS** | 🔴 **Very high** — removes a Google penalty risk and a Swedish marketing-law exposure |
| **Q3** | Fix `ELEGANTEDIA` → `ELEGANTMEDIA` in the business listing data | H7 | **XS** | High |
| **Q4** | Remove the other studio's name (`لُمسة ضوء`) from the page source | H8 | **XS** | High |
| **Q5** | Swedish phone placeholder (`07…` not the Gulf `05…`) | M12 | **XS** | Medium |
| **Q6** | Stop the date field accepting dates in the past | M11 | **XS** | Medium |
| **Q7** | Fix Swedish grammar: "Lättanvänt" → "Lättanvänd" | L7 | **XS** | Low |
| **Q8** | Remove the obsolete `keywords` tag | L10 | **XS** | Low |
| **Q9** | Add a favicon | L8 | **S** | Medium — currently a blank page icon in every tab and bookmark |
| **Q10** | Fix the copyright and placeholder text contrast (3.15:1 and 3.93:1 → both above 4.5:1) | M8, M9 | **XS** | Medium |
| **Q11** | Make the page source Swedish instead of Arabic-labelled-as-Swedish | H8 (part) | **S** | High — what search engines fetch first currently contradicts itself |
| **Q12** | Fix the real contact details | H4, H5 | **XS** | 🔴 **Very high** *(needs N1, N2)* |
| **Q13** | Fix or remove the off-brand social links | H6 | **XS** | High *(needs N4)* |

---

# Foundational fixes

*Structural work. This is where most of the value is.*

### F1 — Make the booking form actually work
**Findings:** C1, M10, H9 · **Effort: M** · **Impact: 🔴 Very high**

The highest-value change on the entire list. Every enquiry submitted today is deleted.

- Wire the form to a real destination (per **D1**)
- Real success *and* failure states — currently there's no such thing as a failed submission because nothing is ever sent
- Announce the result to screen readers (`aria-live`)
- Add the privacy notice and consent the EU requires for collecting names and phone numbers
- Keep the WhatsApp route prominent as a parallel option

*Note: today's broken form is accidentally GDPR-safe because it stores nothing. The moment it works, the privacy notice becomes mandatory — which is why F1 and the privacy work ship together, not separately.*

### F2 — Make the site usable without a mouse
**Findings:** C4, C5, H10 · **Effort: M** · **Impact: 🔴 Very high**

- **Gallery items become real buttons** so all 12 portfolio images can be opened by keyboard. Currently: impossible.
- **Closed mobile menu leaves the tab order** — today focus jumps to invisible links 129px off the side of the screen.
- **A visible focus indicator**, designed as part of the brand rather than bolted on. The gold at 11.85:1 against the background makes a striking, on-brand focus ring — this is a chance to make an accessibility requirement look deliberate.

### F3 — Make the lightbox a proper dialog
**Findings:** H11 · **Effort: M** · **Impact: High**

Announce it as a dialog, move focus into it on open, trap focus while open, return focus to the image that opened it on close. Escape and RTL-aware arrow keys already work and stay as they are.

### F4 — Page structure and landmarks
**Findings:** H12, H13, L5 · **Effort: S** · **Impact: High**

Add `<main>`, add a skip link, fix the heading jump (`h2` → `h4`), give star ratings a text alternative so screen readers stop reading "black star, black star, black star".

### F5 — Translate the interface, not just the content
**Findings:** H14 · **Effort: S** · **Impact: High**

Menu/close/previous/next buttons currently announce in Swedish to Arabic and English users. The About image describes itself in Arabic to Swedish visitors. All of it moves into the translation file.

### F6 — Respect motion preferences
**Findings:** M6, M7, M4, L4 · **Effort: S** · **Impact: Medium–High**

Cover all five animations under `prefers-reduced-motion` (only one of five is covered today — the infinite hero zoom is not). Add proper state announcements to the filter, language and menu buttons. Enlarge the language switcher (currently 27×29px — the smallest control on the site is also one of its best features). Fix the zoom icon that doesn't flip in Arabic.

### F7 — Replace the fabricated numbers
**Findings:** H1, H2 · **Effort: S** · **Impact: High**

The 4-stat bar becomes a smaller trust strip carrying only true statements — 12 years' experience, the services actually offered, languages spoken, delivery commitment. Testimonials handled per **D3**.

**"Languages spoken: Svenska · English · العربية" is a genuine selling point for this business** and a far stronger trust signal than an invented award count.

### F8 — Content and structure for the real audience
**Findings:** M14, M15, H3 · **Effort: M** · **Impact: High**

- **Christenings and church events** added as a service, as a gallery category, and as a search target. `dopfotograf` is a real search term with real intent and no presence on this site today.
- **A "How it works" section** answering the questions a price list normally answers — what happens after you make contact, roughly how long things take, what you receive at the end, and that every quote is tailored to the event. This directly addresses the bounce risk from having no prices, without publishing any.
- **Portfolio copy corrected** — "browse a selection of our work" currently sits above stock photography. The placeholders are fine; claiming authorship of them isn't.

### F9 — Image handling rebuilt
**Findings:** H18, H17, M2, M3 · **Effort: M** · **Impact: High**

Set up now so the client's real photos are a drag-and-drop swap later:

- A real `assets/` structure with consistent aspect ratios
- Width and height on all 17 images (none have them — content jumps as the page loads)
- Responsive sizes so phones stop downloading desktop-sized images
- Modern formats (WebP)
- The hero image preloaded — it's the largest thing on screen and the browser currently can't find it until after the stylesheet is parsed

### F10 — Move the Web Design section to its own page
**Findings:** M13 · **Effort: S** · **Impact: Medium**

Its own page, linked from the footer. The hand-built SVG mockup moves with it — it's good work and it stays. The landing page goes back to having one job.

### F11 — Findability
**Findings:** H15, H16, M16, M17, L9 · **Effort: S–L** *(L only if D2 = B)* · **Impact: High**

- Proper business listing data: `PhotographyBusiness` type, real address, service area, opening hours, social profiles — currently a local business with no location, which can't rank for "photographer near me"
- `sitemap.xml` and `robots.txt` (both missing)
- Social sharing preview that uses the business's own image, not a stock URL
- A branded 404 page
- Per-language titles and descriptions *(scope depends on **D2**)*

---

# Polish

*Visual and brand work. Specified in `BRAND.md`, applied here.*

| ID | Change | Findings | Effort | Impact |
|---|---|---|---|---|
| **P1** | Apply the type scale — replace 11 one-off font sizes with a real scale | — | **M** | Medium–High |
| **P2** | Apply the spacing scale — replace ad-hoc `26px/22px/44px/38px` with a rhythm | — | **M** | Medium |
| **P3** | Move stray greys (`#ddd`, `#ccc`, `#cfcfcf`, `#666`) into named tokens | M8 | **S** | Medium |
| **P4** | **Fix Arabic letter-spacing** — Arabic labels currently get *more* letter-spacing than Latin (3px vs 2px), which breaks the cursive joining Arabic script depends on. It makes the Arabic version look subtly wrong to anyone who reads it | *(new — found during Phase 2)* | **S** | **High for Arabic readers** |
| **P5** | Replace emoji service icons with custom SVG | L6 | **M** | Medium — emoji render as a different picture on every operating system, so the "luxury" section looks different to every visitor |
| **P6** | Remove dead CSS and dead code paths | L1, L2, L3 | **S** | Low |
| **P7** | Defer scripts | L11 | **XS** | Low |
| **P8** | Split font loading per language — Swedish visitors currently download 11 Arabic font faces they'll never see, and vice versa | M1 | **M** | Medium |
| **P9** | Hero sizing fix so the call-to-action isn't pushed below the fold on phones | M5 | **XS** | Medium |

---

# Batch sequence

Each batch = one branch, one preview link, one plain-language summary, one go-ahead from you.

| Batch | Contents | Why this order | Needs |
|---|---|---|---|
| **0** | Backup, git setup, `v0-pre-redesign` tag | Undo must exist before anything else | — |
| **1** | **Q1–Q11** — quick wins | Highest value per minute on the whole list. Canonical tag and fake ratings alone justify the batch | — |
| **2** | **F1** — working booking form | The business is losing leads every day this waits | D1, N3 |
| **3** | **F2, F3, F4, F5, F6** — accessibility foundation | Makes the site usable for everyone. Groups together because they share the same files | — |
| **4** | **F7, F8, Q12, Q13** — content, credibility, christenings | Once the site works, make it honest and complete | N1, N2, N4, D3 |
| **5** | **F9, F10, P8, P9, P7** — images, performance, Web Design page | Structural cleanup before visual polish | — |
| **6** | **P1–P6** — brand system applied | Design last, on a foundation that's sound | — |
| **7** | **F11** — SEO and findability | Last, because it should describe the *finished* site | N5, N6, D2 |

**Batches 1 and 3 need nothing from you and can start immediately.**

---

## Deliberately out of scope

Worth doing, not part of this pass:

- **Real photography** — the single biggest improvement available, and it's the client's to supply
- **Real testimonials** — collect after the next few bookings
- **A custom domain** (`elegantmedia.se`) — more professional than a `github.io` address, and better for search. ~150 kr/year. Worth raising with the client
- **Analytics** — there's currently no way to know how many people visit or where they drop off. A privacy-friendly option (Plausible, Umami) would inform the next round properly
- **A blog / recent-weddings feed** — the strongest long-term SEO play for wedding photographers, but it needs real content first
- **Booking/calendar integration** — only worth it at higher enquiry volume

---

## Honest risk notes

- **D2 (option B) is the only item that adds machinery** to a site that currently has none. It's the right call for reach, but it's the one change that makes the site slightly harder to hand back to a non-technical owner. Scheduled last so it can be dropped without disturbing anything else.
- **Preview links are a workaround, not a proper staging environment.** GitHub Pages doesn't support preview deploys. What I'm providing is faithful for design, content and behaviour, but the final check on search-engine tags has to happen after merge, on the live site.
- **Performance claims need confirming on a real device.** My measurements came from a preview browser where two of the standard speed metrics wouldn't report. Batch 5 should end with a Lighthouse run on real hardware.
- **F9 will need revisiting when real photos arrive.** I'm building the structure now; the actual files land later.

---

**Next step: your approval.** Specifically I need:
- **D1** — how the form should send *(my recommendation: both email and WhatsApp)*
- **D2** — whether the three languages should be findable in search *(my recommendation: yes, scheduled last)*
- **D3** — what to do with testimonials meanwhile *(my recommendation: remove until real ones exist)*
- **N1–N7** — the real contact details, when you can get them. **Batches 1 and 3 don't wait on these.**

See `BRAND.md` for the visual specification that Batch 6 applies.
