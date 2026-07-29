# Vinyl-Oid — CLAUDE.md
*For Trinity. Read before touching anything.*

---

## What Vinyl-Oid Is

Free AI-powered identification tool for vinyl records. Upload a photo of the
label, dead wax, or sleeve — Vinyl-Oid identifies artist, pressing, matrix
numbers, and value. Chat with Uncle Kevin (audiophile, free-festival veteran,
"it's all about the music, not the gear") for hi-fi and record-care advice.

Part of the FeelFamous -Oid Ecosystem.

**Live at:** vinyl-oid.co.uk

---

## The Character

**Uncle Kevin** — mid-50s audiophile, free festival/rave veteran, 420
friendly, PLUR values. Not a snob — a £30 car-boot turntable playing a
beat-up 7" is just as valid as a Quad/SME/Goldring rig if the music moves
you. Encyclopaedic on pressing plants, matrix numbers, Goldmine grading,
hi-fi gear, UK/US/Jamaican labels. `netlify/functions/chat-uncle-kevin.js`.

---

## Stack

- **Static HTML** — single page (`index.html`), no framework, no build step
- **Tailwind CSS CDN** — inline
- **Netlify** — hosting + serverless `/netlify/functions/`
- **Supabase** — users, kudos, leaderboard, broadcast (`pdnjeynugptnavkdbmxh`)
- **Gemini 2.5 Flash** — `chat-uncle-kevin.js` (never Anthropic API)
- **Patreon** — membership OAuth (never Stripe for memberships)

---

## File Map

```
/
├── CLAUDE.md
├── LICENSE                          ← AGPL v3
├── index.html                       ← entire app: Identify, Ask Kevin, Crate Dig, Care, Gear, Meet, Village
└── netlify/functions/
    └── chat-uncle-kevin.js          ← Ask Kevin chatbot (Gemini 2.5 Flash, text-only)
```

**Known gap:** `index.html` calls `/.netlify/functions/analyze-image` (the
Identify/Roast photo tool) and `/.netlify/functions/patreon-auth` (sign-in),
but neither function exists in this repo — only `chat-uncle-kevin.js` is
present. The Identify tab and Patreon sign-in are wired up client-side but
will fail at runtime until those two functions are added. Flag to Chris
before assuming this is a live, working deploy.

---

## Free-to-use philosophy (Chris, 2026-07-13 — read before adding any gate)

The core tool is free for everyone, no sign-in, no lock icon, no "Villager+
only" banner. Don't gate the tool itself behind Patreon.

**What Patreon/paid tiers are for:** genuine extras that cost ongoing hosting/
upkeep and aren't required to use the tool. Frame honestly, never as a
shame-lock ("🔒 ... Unlock →"). No tier-comparison shop windows.

**The ask, when there is one:** one honest, low-key line after the task
completes — free to use, tell a mate if it helped, buy-me-a-coffee if you
want to say thanks (one-off, `buymeacoffee.com/chrispteemagician`), Patreon
if you want to be a regular. Not a gate. Not gamified.

**Repo-specific facts (don't relitigate):**
- Audited `index.html` and `netlify/functions/` for `isPro`, `patron_status`,
  "Villager only", "Founders only", "Elder only", and 🔒 — the only hit was
  `data.isPro` in the Patreon OAuth callback, which only ever sets a status
  badge (`patreonTierLabel`/`userBadge`). No hard gate exists anywhere —
  Identify, Ask Kevin, Crate Dig, Care, Gear, and Meet are all free and
  ungated already.
- No `showUpgradeModal` state, no false-scarcity banner ("first 1,000
  only... door goes up") found. Pricing was already correct (see table
  below) as of commit `8df2cc4`.
- Added the one thing genuinely missing: a low-key honesty-box message in
  `#resultView`, shown after every identification result, above the
  sign-in prompt — Buy Me a Coffee (one-off) + Patreon (ongoing) links.
- Fixed one voice/tone leftover: the footer tagline "World domination
  through kindness. One ember at a time." → "Just trying to be useful. One
  ember at a time." — same ecosystem-wide softening already applied in
  radi-oid, sail-oid, dnb-santa, and others.
- The `.founding-banner` top banner and CSS class name are stale in name
  only — its actual copy ("It's free to use, always will be... if you want
  to chip in") is already honest, no scarcity language, left as-is.

Full doctrine + mechanical pattern: DocBrain `tech/free-to-use-degate-skill.md`.
This same pattern has rolled out across motor-oid, cannabin-oid,
spicylister, radi-oid, sail-oid, travel-oid, dnb-santa, stamp-oid,
miniature-oid, magic-oid-v3, and Retro-oid — check those CLAUDE.md files
for the shared version before assuming this file is the only place it
applies.

---

## Membership Tiers (Patreon — chrisptee campaign)

| Tier | Price | Perk |
|------|-------|------|
| 🏡 Villager | £4.95/mo | Hut in the village, kudos & leaderboard, activity feed |
| ⭐ Elder | Earned, not bought | Everything in Villager + mini hamlet page + named in the village roll |
| 👑 Founder | £14.95/mo | Full hamlet suite, direct line to Chris, early access, 300 kudos on joining |

All Patreon links go to `https://www.patreon.com/chrisptee`.

---

## Gemini API Rules (Ecosystem-Wide)

Two known pitfalls — always check for these:

1. **Do NOT set `thinkingBudget: 0`** — Gemini 2.5 Flash rejects it with a
   silent 400. Omit `thinkingConfig` entirely. (`chat-uncle-kevin.js` has no
   `thinkingConfig` — clean.)
2. **Do NOT hardcode `mime_type: "image/jpeg"`** — always extract the real
   type from the data URL first. Not applicable yet in this repo —
   `chat-uncle-kevin.js` is text-only; the missing `analyze-image.js` will
   need this the moment it's written:
   ```js
   const mimeMatch = image.match(/^data:(image\/[\w+.-]+);base64,/);
   const mimeType = mimeMatch ? mimeMatch[1] : 'image/jpeg';
   const rawImage = image.replace(/^data:image\/[\w+.-]+;base64,/, '');
   ```

---

## Voice & Tone (read before writing any outward-facing copy)

State the plain fact once, let it carry the weight. No cast villain, no
combat verbs (fight, arm yourself, disrupt), no word bigger than what's
true (domination, extraction, manifesto, revolution). Uncle Kevin's own
voice (mate/bruv/proper, PLUR, anti-snob warmth) is a character, not
marketing copy — leave it alone.

Full rationale/history: DocBrain `concepts/ecosystem-voice-and-tone.md`.

---

## Deploy

Push to `main` → Netlify auto-deploys. Never drag-to-Netlify. `git pull`
before every push.
