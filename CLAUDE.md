# Highland Music LLC — Portfolio Ops

This repo is two things:
1. The public marketing site (`index.html`, `support.html`, `privacy.html`, `styles.css`, `CNAME`) served via GitHub Pages at **highlandmusicapps.com**.
2. The **portfolio-level Claude Code memory hub** for Highland Music, LLC's apps — business/legal status, shared infrastructure decisions, and cross-app lessons that don't belong in any single app's repo.

Each app has its own repo with its own `CLAUDE.md` for app-specific architecture/code/working memory. This file is for things that span apps or live above the code: entity/legal status, App Store account setup, shared vendor decisions (RevenueCat, PostHog, Codemagic, sample audio sources), and portable technical gotchas worth knowing before hitting them again in another app.

**When to use this repo/session vs. an app repo:** start a session here for business/legal/account-level questions ("where do we stand on Apple Organization conversion?", "what's our IAP vendor across apps?") or when a lesson learned in one app should be remembered for the others. Use the app's own repo for anything about that app's code.

## Apps
- **Jazz Guitar Lab** — `pablohoney1977/jazz-guitar-app`. Freemium iOS app, jazz guitar harmony trainer. Furthest along — see its own CLAUDE.md for full status.
- **Piano Chords Lab** — repo not yet created/added to this session.
- **Note Quest** — repo not yet created/added to this session.

All three ship under bundle IDs on the `com.pablohoney.*` namespace, single-file React PWA + Capacitor pattern, Codemagic CI/CD, no Mac required.

## Business / Legal Status
> Not legal advice — confirm specifics with a small-business/IP attorney or CPA.

- **⚠️ EXACT LEGAL NAME: `Highland Music LLC` — NO COMMA.** The WI Articles of Organization read exactly that. Older notes (and this file, before 2026-07-15) said "Highland Music, LLC" with a comma — **wrong for legal/tax/banking records**. The comma is fine in prose; EIN/W-9/bank/Apple records must match the state filing character-for-character. The site's pages were corrected 2026-07-15.
- **Entity:** `Highland Music LLC` — WI **Entity ID `H079857`**, filed **12/8/2024**, Ch. 183, single-member → **disregarded entity** (no S-corp election). Sole member/responsible party: Paul W Ehresmann. The LLC only shields personal assets if it's the actual seller/operator of each app.
- **✅ Apple Developer account: CONVERTED TO ORGANIZATION 2026-07-15 — `Highland Music LLC` is the seller.** The liability-shield goal is achieved on Apple's side. This was the long-running blocker; **it's closed — don't re-open it or send the user chasing D-U-N-S/website requirements again.** (Historical: Apple required a D-U-N-S number, a public LLC-associated website, and an on-domain work email; this site satisfied the website/domain piece.)
- **EIN: applied for, PENDING (as of 2026-07-15).** SS-4 **faxed 2026-07-14** to **855-641-6935** (50-states number, verified against irs.gov); **CP 575 arrives BY MAIL ~mid-Aug 2026** (return-fax box left blank deliberately — free send-only fax, chose free-and-slower).
  - **Two dead ends — do NOT re-suggest either:** (1) the IRS **online EIN assistant hard-fails with reference 101** (name-conflict flag; LLC filed 12/2024 so it's not a state-sync timing issue and won't self-clear); (2) **the phone loops** — 1-800-829-4933 tells domestic callers "online only" and points back at the tool that 101'd, because **IRS discontinued domestic phone EIN issuance** (267-941-1099 is international-only). **Fax is the only working path** (a human can clear a 101).
  - When the CP 575 arrives: **save + back up the PDF.** Reissue otherwise needs a 147C letter.
- **Bank account: BLOCKED on the EIN.** Open a dedicated **LLC business checking** account — free options **Mercury / Relay / Bluevine** ($0/mo, no minimum). **E\*TRADE has no real business checking** (brokerage only) — wrong tool for payouts. Requires the **EIN**, not the SSN.
  - **Decision 2026-07-15: do NOT bridge payouts via personal banking.** Pre-launch = no revenue flows, so it buys nothing; and now that the **seller is the LLC**, a personal bank + personal-SSN W-9 is an **entity mismatch** risking an Apple tax-info review / payment hold. Only reconsider if the **Paid Applications Agreement** (needs banking + tax info active before you can sell anything) hard-gates IAP work on the critical path — open question being checked in App Store Connect.
- **Sequence from here:** `CP 575 arrives → open LLC checking → App Store Connect banking + W-9 and RevenueCat payout under the LLC/EIN → Paid Applications Agreement goes Active → can sell.`
- **Apple Small Business Program** (15% cut instead of 30%, raises net per $14.99 sale from $10.49 to $12.74) is independent of Individual vs Organization — enroll separately, anytime, under $1M/yr revenue.
- **Domain:** `highlandmusicapps.com` (Porkbun, canonical). `.net` also mentioned as available/considered — not yet secured or redirected.
- **Maintaining the liability shield:** separate LLC bank account, no commingling, LLC owns the apps/IP (and ideally the RevenueCat account + domain). **Commingling is the #1 veil-piercing fact** — fund the LLC account with a documented "owner capital contribution", pay yourself via **owner's draws**, never swipe the LLC card for personal. LLC does not shield against the owner's own personal wrongful acts.

## Shared Infrastructure Decisions (apply across apps unless an app overrides)
- **IAP:** RevenueCat (`@revenuecat/purchases-capacitor`), one-time non-consumable unlock per app (no subscriptions — one-time price is the chosen model across the portfolio).
- **Analytics:** PostHog, CDN snippet pattern, no-op until a real project key is set.
- **CI/CD:** Codemagic (cloud build, no Mac required), config per-repo in `codemagic.yaml`.
- **Native wrapper:** Capacitor (web → native), `ios/` project committed per app repo.
- **Audio samples:** sourced from `nbrosowsky/tonejs-instruments` (used by Jazz Guitar Lab; check here before re-sourcing samples for another app).
- **Support:** simple on-domain email channel (`support@highlandmusicapps.com`) via this site's `support.html` — shared across all apps, no per-app support infra.
- **Marketing:** organic only (ASO, YouTube, relevant subreddits/forums, community). No paid ads — one-time IAP economics don't support paid acquisition across any of these apps.

## Cross-App Technical Lessons (portable — check before debugging the same class of bug twice)
- **iOS Capacitor local-asset fetch returns `status 0`:** on `capacitor://localhost`, `fetch()` of bundled local assets (e.g. audio samples) resolves with HTTP `status 0`/`ok:false` even though the bytes are valid — NOT an error. Code that does `if(!r.ok) return null` will silently discard good local assets on every native build. Fix pattern: accept `status 0` (`if(!r.ok && r.status!==0) return null;`), then guard on actual `byteLength` so a true 404 still fails. Web/PWA behavior (real 200s/404s) is unaffected. First hit + fixed in Jazz Guitar Lab (commit c50abff); check for the same pattern in any app that fetches bundled assets at runtime instead of importing them.
- **iOS Safari doesn't repaint filtered SVGs on attribute change:** an SVG element using a `filter` (e.g. a blur/glow) won't visually update when React changes its attributes on real iOS Safari/WebKit — stays stale until a user gesture forces reflow. Chromium (and headless Playwright) does NOT reproduce this, so it can't be caught by smoke tests. Fix: force a GPU compositing layer with `style:{transform:'translateZ(0)', WebkitTransform:'translateZ(0)'}` on the affected `<svg>`. Apply proactively to any new filtered SVG in any app that re-renders on state change.

## App Store Submission Checklist (per-app template)
Track each app's progress through this list in that app's own CLAUDE.md; this is the shared shape.
- [ ] Capacitor iOS project initialized
- [ ] Codemagic pipeline configured
- [ ] Bundle ID registered in Apple Developer portal
- [ ] App Store Connect API key in Codemagic
- [ ] App Store listing (name, screenshots, description, pricing)
- [ ] IAP product(s) set up in App Store Connect + RevenueCat project/key
- [ ] App icon (all required sizes) + splash screen
- [ ] First TestFlight build
- [ ] Internal testing
- [ ] App Store submission

## Working Memory (portfolio-level checkpoint)
> Rolling snapshot, overwrite don't append. Last updated: 2026-07-15.

- **✅ APPLE ORG CONVERSION DONE + EIN FAXED (2026-07-15):** Two structural blockers moved. (1) **The Apple Developer account is now converted to Organization — `Highland Music LLC` is the seller.** The D-U-N-S/website/domain-email thread that dominated this file is **closed**; the site did its job. (2) **EIN faxed 2026-07-14, CP 575 arrives by mail ~mid-Aug 2026** — the IRS online tool 101s and the phone is a dead loop, so **fax is the only path** (details in Business/Legal above). Also corrected the **exact legal name to `Highland Music LLC` — no comma** (per the WI Articles) across this site's `index.html`/`support.html`/`privacy.html` and both CLAUDE.md files. **Next:** CP 575 → free LLC checking (Mercury/Relay/Bluevine) → Apple/RevenueCat payout under the LLC EIN. **Open question:** does the Paid Applications Agreement (needs banking+tax active) block creating/testing the `pro_unlock` IAP? Being checked in App Store Connect → Business; if it blocks, the ~4wk EIN wait is on the critical path and we reweigh the personal-banking bridge (currently decided against).
- **Repo created (2026-07-12):** this file added to the existing `highland-music-site` repo (rather than spinning up a new `highland-music-ops` repo — GitHub App integration for this session isn't authorized to create new repos, 403; reusing the site repo avoided that and keeps one fewer repo to manage) to serve double duty as the LLC's public site + the portfolio ops/memory hub. Seeded from Jazz Guitar Lab's CLAUDE.md — cross-checked details (Apple Individual→Org status, D-U-N-S gate, domain/site live status, shared IAP/analytics vendor choices, the two portable iOS gotchas) as of that app's 2026-07-10/07-05 checkpoints. Piano Chords Lab and Note Quest repos don't exist yet in this session — add them here once created so this file can track all three.
- **Leftover nice-to-haves (no longer gating anything):** Porkbun email forwarding for `support@highlandmusicapps.com` → Gmail (MX already present, rule not set up); Porkbun registrant Organization field = `Highland Music LLC`. These fed the Apple conversion, which is now done — do them for tidiness, not urgency.
