# Bagpipe Lab — Product Strategy

> Working name "Bagpipe Lab" (placeholder, matches the "X Lab" naming pattern of Jazz Guitar Lab / Piano Chords Lab). No app repo exists yet — this doc lives in the portfolio hub until one is created, same pre-repo state as Piano Chords Lab and Note Quest. See root `CLAUDE.md` for portfolio-wide business/infra decisions this app inherits by default (RevenueCat one-time IAP, PostHog, Codemagic, Capacitor).

## Core insight the curriculum is built on

A Highland bagpipe chanter has no tonguing and no way to silence or restart a note — it's one continuous reed tone. That means grace-note embellishments (starting with the simplest, the doubling) aren't decoration layered on top of a tune; they're the *only* mechanism a piper has for separating two repeated notes rhythmically. This gives a concrete, motivated answer to "when do embellishments stop being a fun hook and start being a distraction from fundamentals": introduce the doubling immediately after the student can hold steady rhythm on an unembellished simple tune, framed as solving a problem they've already felt (their repeated notes blur together), not as an early flourish to hold their interest.

## Product differentiator vs. existing bagpipe learning material

Existing options are tutor-book PDFs (College of Piping, National Piping Centre series), YouTube channels, and static fingering-chart/metronome apps. None combine an animated, interactive chanter with a rhythm-driven play-along loop.

That gap is workable because chanter fingering is **deterministic** — one fingering produces exactly one pitch, no embouchure or pitch-bending like other wind instruments. So unlike pitch-matching apps (likely including this portfolio's other two apps), the skill actually worth grading is **timing and finger-sequence accuracy**, not pitch. Core gameplay loop: an animated SVG chanter with holes lighting up in sequence, played against a falling-notes/rhythm-lane UI synced to a backing play-along track — closer to a rhythm game than an ear-trainer.

## Curriculum phases

1. **Meet the chanter** — interactive hole diagram (8 holes, hand position top/bottom), tap-to-hear each of the 9 notes (Low G, Low A, B, C, D, E, F, High G, High A).
2. **The scale** — full octave play-along at slow tempo, visual finger-highlight leads eye/ear, ascending and descending.
3. **Steady rhythm, no embellishments** — simple traditional (public-domain) melodies, scored purely on timing/fingering-sequence accuracy. No ornaments yet — this phase's checkpoint gates phase 4.
4. **The doubling** — introduced with the functional "why" above, immediately practiced on the repeated-note passages from phase 3's tunes.
5. **First real tunes** combining scale + doubling (e.g. simplified traditional pipe tunes).
6. **Progressive embellishments** (strike, grip, throw-on-D, taorluath, birl) — each introduced in the specific tune context that requires it, each gated behind a rhythm-accuracy checkpoint, so a student can't chase ornaments past their fundamentals.

## Monetization

Free tier: phases 1–3 (scale + rhythm fundamentals) — enough to hook a genuine beginner and prove the interaction model. Paid one-time unlock: phases 4–6 (tunes + embellishments), mirroring Jazz Guitar Lab's freemium boundary and this portfolio's one-time-IAP-only policy (see root `CLAUDE.md`).

## Market note

Pipers are a smaller, more niche audience than guitar/piano players — lower download ceiling, but less competition and a more committed audience willing to pay for correct embellishment teaching. Organic marketing channel: piping-specific communities (r/bagpipes, pipe band forums, College of Piping / National Piping Centre adjacent audiences) rather than general music-app ASO.

## Open questions for next session

- Practice chanter only, or also model full Highland pipes (drone sound, but no separate fingering)?
- Audio: recorded practice-chanter samples (check `nbrosowsky/tonejs-instruments` first per portfolio convention) vs. synthesized reed tone.
- Rhythm-accuracy input: tap/metronome-sync only, or attempt mic-based onset detection for real practice-chanter timing?
- Tune selection and copyright/public-domain status per tune before committing to a repertoire list.
