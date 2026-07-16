# Highland Music, LLC — Portfolio Ops

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

- **Entity:** Highland Music, LLC already exists. The LLC only shields personal assets if it's the actual seller/operator of each app — an Apple **Individual** developer enrollment publishes under the owner's personal name, no entity shield.
- **Apple Developer account:** Enrolled as **Individual** (jazzguitarlab's dev account). Needs conversion to **Organization** (LLC as seller) before first submission, for the liability shield. It's an in-place conversion — no new account, no second $99.
  - Requirements confirmed via Apple's enrollment docs: (1) D-U-N-S number for the LLC (free, ~1-2 weeks, apply first — long-pole item), (2) a public website clearly associated with the LLC (bare landing page/social-only rejected), (3) a work email on that same domain (not Gmail).
  - **Website requirement: DONE.** This site is live at highlandmusicapps.com over HTTPS, LLC legal name "Highland Music, LLC" in every footer, on-domain contact `support@highlandmusicapps.com`.
  - **Still needed:** Porkbun email forwarding for `support@highlandmusicapps.com` → Gmail (MX already present, forwarding rule not yet set up); Porkbun registrant Organization field set to "Highland Music, LLC"; then apply for D-U-N-S; then request the Individual→Organization conversion (Apple Developer site → Contact us → Membership and Account → Program Enrollment).
  - Do the conversion **before** any app's first submission — converting after there's a live app + live IAP is a messier app-transfer path (IAP restrictions, not easily reversible).
- **Apple Small Business Program** (15% cut instead of 30%, raises net per $14.99 sale from $10.49 to $12.74) is independent of Individual vs Organization — enroll separately, anytime, under $1M/yr revenue.
- **Domain:** `highlandmusicapps.com` (Porkbun, canonical). `.net` also mentioned as available/considered — not yet secured or redirected.
- **Maintaining the liability shield:** separate LLC bank account, no commingling, LLC owns the apps/IP (and ideally the RevenueCat account + domain). LLC does not shield against the owner's own personal wrongful acts.

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
> Rolling snapshot, overwrite don't append. Last updated: 2026-07-12.

- **Repo created (2026-07-12):** this file added to the existing `highland-music-site` repo (rather than spinning up a new `highland-music-ops` repo — GitHub App integration for this session isn't authorized to create new repos, 403; reusing the site repo avoided that and keeps one fewer repo to manage) to serve double duty as the LLC's public site + the portfolio ops/memory hub. Seeded from Jazz Guitar Lab's CLAUDE.md — cross-checked details (Apple Individual→Org status, D-U-N-S gate, domain/site live status, shared IAP/analytics vendor choices, the two portable iOS gotchas) as of that app's 2026-07-10/07-05 checkpoints. Piano Chords Lab and Note Quest repos don't exist yet in this session — add them here once created so this file can track all three.
- **Next concrete step:** Porkbun email forwarding for `support@highlandmusicapps.com`, then Porkbun registrant Org field, then D-U-N-S application — all gating the Apple Individual→Organization conversion for Jazz Guitar Lab's first submission.
