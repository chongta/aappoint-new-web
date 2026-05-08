# AAppoint New Web — Marketing & Demo Site

## Project Overview

Static HTML/CSS/JS marketing website for the AAppoint platform. No build system, no framework, no npm — pure browser-native code. Three self-contained pages:

- **[index.html](index.html)** — Dark-themed segment picker (Thailand Dining vs HK Wellness)
- **[aappoint.html](aappoint.html)** — Thailand restaurant product page (default lang: Thai)
- **[aappoint-wellness.html](aappoint-wellness.html)** — Hong Kong wellness product page (default lang: Traditional Chinese)

## Architecture

### No Build Step
Edit files directly. Open in browser to preview. No `npm install`, no Vite, no Webpack.

### i18n System
All user-facing strings use `data-i18n="key"` attributes on elements. Each page has an inline `<script>` block at the bottom with a `STRINGS` object keyed by language code (`th`, `en`, `zh-TW`). The `applyLang(lang)` function iterates all `[data-i18n]` elements and swaps text. Language switcher buttons (`.lang button`) toggle the active state and persist nothing (no localStorage).

When adding new copy:
1. Add `data-i18n="your_key"` to the element
2. Add `your_key` to all three language objects in `STRINGS` (even if two languages share the same text)

### Demo Flow (5 Steps)
Each page has an interactive demo stepper (`.demo-stage`):
1. Enter venue name + city → triggers `lookupVenue()`
2. Loading state → Google Places API fetch → show venue card
3. Pick party size / service + date + time (hardcoded mock slots)
4. Confirmation summary
5. Merchant sign-up CTA (email + phone inputs, non-functional)

Steps are controlled by `goStep(n)` which toggles `.pane.on` and `.step.on`/`.step.done` classes. The demo saves nothing — it's a pure UX mockup.

### Vote / Wishlist Section
Venue cards rendered from a hardcoded `VENUES` array in JS. Each card has a vote counter (stored in a local `votes` object, resets on page reload). Casting a vote opens the post-vote modal (`#voteModal`) which embeds `agentgita.com` in an iframe. If the iframe is blocked by `X-Frame-Options`, a fallback overlay appears with a direct link.

## Brand Tokens (CSS Custom Properties)

```css
--pink:     #EC1A78   /* primary CTA, accent */
--teal:     #14787C   /* brand dark teal */
--teal-2:   #1AAEA8   /* brand light teal, checkmark */
--ink:      #0b0d1a   /* near-black text */
--ink-2:    #3b3f55   /* secondary text */
--muted:    #8a90a4   /* placeholder / meta text */
--line:     #ececf2   /* borders */
```

The AAppoint logo SVG is inline on every page — the pink "A◯|" mark with a teal time-checkmark stroke. Do not replace it with an `<img>` tag; keep it inline for color theming.

## Page-Specific Notes

### aappoint.html (Thailand / Dining)
- Step 3 shows **party size** picker + date/time grids (restaurant-style)
- Distribution channels: Google, Michelin, TripAdvisor, ShopeePay, Stripe
- Pricing: 2-tier (฿399 Starter / ฿1,999 Pro, annual)
- Cross-sell footer card links to `aappoint-wellness.html`

### aappoint-wellness.html (Hong Kong / Wellness)
- Step 3 shows **service selector** (svc-btn chips) + date/time grids (wellness-style)
- Verticals section: 8 category cards (Spa, Massage, Clinic, Dental, Gym, Yoga, Hair/Nail, Aesthetics)
- Payment channels: Octopus, AliPay+, WeChat Pay HK, Reserve with Google
- Pricing: 2-tier (HK$99 Lite / HK$888 Premium, annual)
- Cross-sell footer card links to `aappoint.html`

## What Is and Isn't Wired Up

**Functional (client-side only):**
- Language switcher
- Demo stepper navigation
- Google Places API call in `lookupVenue()` — uses a hardcoded API key in the JS; results populate venue name/address/rating/hours
- Vote buttons — increment local counters, open modal
- Post-vote modal iframe + fallback

**Not functional (UI mockup only):**
- "Open restaurant" sign-up form (step 5 email/phone inputs)
- `finalCelebrate()` — shows a confetti/success animation but submits nothing
- Vote counts — reset on reload, not persisted anywhere

## Common Tasks

**Change a copy string in all languages:**
Search for `data-i18n="the_key"` in the file, then find `the_key` in the `STRINGS` object near the bottom of the same file and update all three language entries.

**Add a new vote venue card:**
Find the `VENUES` array in the `<script>` block and add an object with `{ emoji, name, area, votes, live, url }`. If `live: true`, the card shows a teal "LIVE" pill and the vote button becomes a booking link.

**Change pricing tiers:**
Find the `.tier-grid` section in HTML and update `.tier-price` and `.tier ul` list items. Also update the matching `STRINGS` keys (`t1_f*`, `t2_f*`, etc.) for all languages.

**Adjust demo mock time slots:**
Find `TIMES` array and `DATES` generation logic in the `<script>` block. Dates are generated dynamically from `new Date()` so they always show the next 4 days. Times are hardcoded strings.
