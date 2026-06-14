# Build Log — frazierideas

## 2026-06-13 — Launch frazierideas.com (the idea-factory launchpad)

**Prompt:** "O SHIT. i bought frazierideas.com and registered frazier ideas llc as an llc.... LETS GO... make me a great landing page linkign to all my live stuff. GO. GO big. Be bold. be fun." (Then, after a mid-session computer crash and recovery: aesthetic = "Maximalist & playful" — option 3 from the pre-crash session; voice = "the idea factory"; include an in-progress "workshop" section.)

**Problem:** Frazier Ideas LLC is the new top-level holding company sitting *above* Fairpoint (itself a 12-site hub) and the standalone apps. It had a domain and an LLC but no home page. Needed a bold, fun launchpad linking everything that's actually live — with zero dead links.

**Solution:**
- **Single-file static page** (`index.html`, no build step — same pattern as every Fairpoint site). Maximalist & playful evolution of the Fairpoint neobrutalist DNA (Space Grotesk, hard offset shadows, purple/lime/coral/gold tokens, bouncy `cubic-bezier` hovers), turned up to 11:
  - Oversized outlined `FRAZIER IDEAS` wordmark (`-webkit-text-stroke` + stacked `text-shadow`), four floating sticker badges ("ships fast", "made in PDX", "est. 2026", "1 person · 1 LLC").
  - Word-rotator tagline: *"a one-person internet **idea factory**"* → quiz machine → app studio → tinkering den → ship-it shop. Sub: "Small software, strong opinions."
  - Stats strip: `2` apps · `12+` quizzes & tools · `400+` scenarios · `1` idea factory.
  - **The good stuff (live):** 2-up cards for Tight Burrito + PlanShopEat, plus a full-bleed purple **Fairpoint** feature card with a giant "12" watermark → fairpoint.website.
  - **In the workshop:** dashed teaser cards (no links) for Pothole Reporter, BikeRouteNicePDX, Trip Shift.
  - Data-driven render (`STATS`/`APPS`/`WORKSHOP` arrays + `textContent`-only DOM building, no innerHTML injection of data). Accessible: `aria` labels, real contrast on every fill, `prefers-reduced-motion` kills all animation. Responsive single-column at ≤640px.
- **Shipped:** git init → `github.com/Paulfrazier/frazierideas` (gh) → `vercel deploy --prod` → Namecheap DNS via `../namecheap/nc.py` (apex `A @ → 76.76.21.21`, `www → cname.vercel-dns.com`, parking records dropped). DNS edge propagated in ~13.5 min; HTTPS cert auto-provisioned. Live at https://frazierideas.com (200).

**Key decisions:**
- Kept the exact Fairpoint color/shadow tokens for family resemblance, but changed layout (2-up grid, full-bleed feature card, scattered stickers) so the parent brand reads as its own louder thing rather than a 13th quiz.
- Linked **only live** properties; in-progress projects are unlinked teasers so there are no dead ends.
- **Recon corrected memory:** an Explore agent flagged Tight Burrito as "not live," but `tightburrito.com` returns 200 — it's a client-rendered SPA, so headless fetch only sees the title shell. Verified by HTTP status, not by scraped text.
- **planshopeat.app is broken** (see below) → linked the working `planshopeat.vercel.app` instead of the brand apex, to avoid a dead link. Flagged to the user as a separate fixable issue.

**Verification:** Rendered in the gstack headless browser at desktop (1280) + mobile (390) — wordmark, stickers, stats, cards, feature card, and workshop teasers all correct, no console errors. Confirmed live production render on https://frazierideas.com (h1 + 2 cards + correct hrefs). All four outbound links return 200 (tightburrito.com, planshopeat.vercel.app, fairpoint.website, and the apex itself).

**Found (out of scope, pre-existing):** `planshopeat.app` resolves to Vercel's IP (`76.76.21.21`) but is **not attached to any Vercel project**, so there's no TLS cert and the SSL handshake fails in a browser (only `planshopeat.vercel.app` works). Fix = add `planshopeat.app` to the PlanShopEat Vercel project so the cert issues, then point this launchpad's PlanShopEat card back at the brand domain.

**Changed files:** `index.html`, `.gitignore`, `BUILD_LOG.md`
