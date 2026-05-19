# Session Handoff — 2026-05-19

Context for the next Claude session. Read this first.

## Who is the user

Angelito C. Paa — Software Developer at Easycom Japan Philippines (Dec 2024 – present), 4+ years total experience. Based in Makati City, Metro Manila. Backend-focused full-stack (Node.js/Express/NestJS + Python/FastAPI), with Android/Kotlin + React Native background. Has run production payment switch (Java FEP, ISO 8583, VisaNet).

**Targeting:** Mid-level (IC2-IC3) primarily, open to early-Senior (Sr. I) as stretch. Not Staff/Principal/Lead/EM/Director.
**Comp target:** PHP 75,000-100,000 / month total.
**Work setup:** On-site / hybrid Metro Manila OR remote PH-based companies (both score 5.0); remote global = 4.0 if comp competitive.

## What was set up in this session

- `config/profile.yml` — full profile with corrected phone (+639691128377), PH archetypes, PHP comp, no-sponsorship visa status
- `modes/_profile.md` — adaptive framing per archetype, PHP negotiation scripts, **Seniority Policy section** with scoring rules (mid 3-5 YoE = 5.0, Senior I = 4.0, 7+YoE/Staff = SKIP, Junior = SKIP)
- `portals.yml` — fully PH-tailored:
  - `location_filter`: PH cities + Remote/APAC/Worldwide allowed; "US only"/India hubs blocked
  - `title_filter.positive`: Backend, Node.js, NestJS, Python, FastAPI, Full Stack, Software Engineer, React Native
  - `title_filter.negative`: includes Staff/Principal/Tech Lead/EM/Director/Head/VP/CTO (blocks above-target seniority)
  - `tracked_companies`: 25 PH-focused (Maya, GCash, PayMongo, Coins.ph, Kumu, Sprout, etc.) + 4 global remote (Canonical, Toptal, Automattic, GitLab)
  - `search_queries`: 16 PH-focused (Kalibrr, Jobstreet PH, Glints, LinkedIn PH, OnlineJobs.ph, Indeed PH) + remote boards

## Scan results so far (48 offers in `data/pipeline.md`)

**Layer 1 — zero-token (scan.mjs):** 22 Canonical jobs. Mostly Worldwide remote, Python/Go heavy.
**Layer 2 — Playwright on 21 PH companies:** 26 jobs added.
**Layer 3 — WebSearch:** Not yet run — was blocked by missing WebSearch/WebFetch permission.

### Top fits (Mid → early-Senior, Backend/Full Stack)

**PH on-site/hybrid (score 5.0 — top priority):**
- Maya: 2× Software Engineer + 4× Senior Software Engineer (incl. Golang)
- GCash: Software Engineer + Senior Software Engineer
- PayMongo: Full Stack Engineer (Taguig hybrid)
- First Circle: Backend Engineer (Manila hybrid)
- NCS Philippines: Cloud Backend Engineer + Software Engineer Backend/Node.js (Taguig)

**Remote PH-friendly:**
- Cloudstaff: Full Stack / Senior Software Developer (WFH)
- Automattic: Experienced Software Engineer + Performance Engineer Backend (fully remote)
- Toptal: 8× Senior contractor roles (Backend/Full Stack/Platform) — Toptal screening model, all "Senior"

**Caveats / verify before applying:**
- Modulus Labs (San Juan) — title says Senior Backend Developer but it's zkML/Web3. User's title_filter.negative has "Blockchain"/"Web3" but the title was clean. Verify before applying.
- GitLab + Thinking Machines — added as section links, not specific jobs; require manual browse (Show all button needs click-through)
- 7 of 8 Toptal listings are "Senior" — borderline stretch for mid-level target. Toptal is also a contractor screening platform, not direct hire.

### Career pages confirmed BROKEN (clean up in portals.yml)

- Coins.ph (404)
- UnionDigital Bank (domain expired/for sale)
- Kumu (404)
- Sprout Solutions (404)
- Foodpanda (PH filter 404)
- Lazada `careers.lazada.com` (DNS unresolved)
- Pearl Talent (no public board)

Either remove these or find working URLs.

## Pending decisions

1. **WebSearch + WebFetch permissions** — need to add to `.claude/settings.local.json` to unblock Layer 3 scan (Kalibrr/Jobstreet/LinkedIn-PH/etc., probably 50-100 more leads). Current allow list only has Playwright. Suggested addition:
   ```json
   "WebSearch",
   "WebFetch"
   ```
2. **Next action** — user was deciding between:
   - (a) Approve WebSearch and finish the scan
   - (b) Skip Layer 3 and evaluate the 48 offers already in pipeline with `/career-ops pipeline`
   - (c) Single deep-dive on one top offer (Maya, GCash, PayMongo, First Circle) as a quality check before batch
   - (d) Clean up broken portals.yml entries first

## File locations (project was moved here)

- Project root: `/Users/angelito/personal/career-ops`
- Pipeline: `data/pipeline.md`
- Scan history: `data/scan-history.tsv`
- Permissions: `.claude/settings.local.json`

## Key files to read on resume

1. This file (`NOTES.md`)
2. `data/pipeline.md` — see what's queued
3. `data/scan-history.tsv` — see what's been skipped/why
4. `modes/_profile.md` — confirm seniority policy is what the user wants
