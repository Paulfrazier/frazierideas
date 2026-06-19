# Build Log — frazierideas

## 2026-06-19 — Add /status sub-page (the workshop log)

**Prompt:** "cool on [frazierideas].com i want a sub page that gives the project status. build notes, progress. both for me to be able to see, and to show to others." (Decisions chosen via follow-up: scope = *all projects, with a public/private split*; maintenance = *curated data array*, no build step.)

**Problem:** The landing page only links **live** properties and shows in-progress work as link-less teasers. There was no single place to see real status — what's live vs building vs planning, how far along each thing is, and what actually shipped recently. Needed a page that works as both a personal command-center and something presentable to others, without leaking sensitive details of the private repos.

**Solution:**
- **New single-file `status.html`** (no build step, same Fairpoint/Frazier neobrutalist DNA as `index.html`: Space Grotesk, hard offset shadows, purple/lime/coral/gold tokens, bouncy hovers). Reachable at `/status` via clean URLs.
- **Curated `PROJECTS` data array** (9 projects) — each with: status (`live`/`beta`/`building`/`planning`), **public/private** badge, a 0–100 **progress bar**, monospace **tech-stack chips**, live/repo **links**, and a collapsible **"Build notes"** changelog (dated milestones) — this is the "build notes, progress" the user asked for.
  - Projects: PlanShopEat, Tight Burrito, Fairpoint, Pothole Reporter, BikeRouteNicePDX, PDX Traffic Map, Backplan, Trip Shift, Travel Price Tracker. (Old forks `teach-me`/`speed-camera` excluded — not Paul's work.)
  - **Public/private split, safely:** private repos (PlanShopEat, Pothole Reporter, Trip Shift, Travel Price Tracker) are marked Private and kept high-level — no API internals, secrets, or reverse-engineering details — so the page is safe to show anyone.
  - **Auto stats + filters:** the top stat strip (total / live / in-progress / on-the-bench) and the status filter bar are computed from the data, so they stay correct as the array changes. Filter buttons re-render the list client-side.
  - Data-driven render with `textContent`-only DOM building (no innerHTML injection of data). Accessible (`aria-pressed` filters, real contrast), `prefers-reduced-motion` kills animation, responsive single-column at ≤560px.
- **`vercel.json`** added (`cleanUrls: true`, `trailingSlash: false`) so the page serves at `/status`.
- **Linked from home:** a pill button under the hero subtitle ("📡 See what's cooking — live status & build notes →") plus a `status` link in the footer.

**Key decisions:**
- **Curated array over auto-scraping BUILD_LOG.md files** (user's choice): keeps the no-build-step pattern, gives full control over what's public, and avoids dumping raw dev notes (some from private repos) onto a public page.
- **Per-project Public/Private badge** so the page doubles as a visibility audit — the same question that kicked off this session.
- To update: edit the `PROJECTS` array (and bump `LAST_UPDATED`) in `status.html`. No build, just `vercel deploy --prod`.

**Verification:** Rendered both pages in the gstack headless browser (desktop 1280 + mobile 375). status.html: 9 cards, 4 computed stats, "Last updated June 19, 2026", no new console errors; the Live filter narrows to 5 cards; Build-notes `<details>` expands to dated entries; progress bars animate to width. index.html: status pill present and points to `/status`, no regressions.

**Changed files:** `status.html` (new), `vercel.json` (new), `index.html`, `BUILD_LOG.md`

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
