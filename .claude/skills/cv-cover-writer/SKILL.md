---
name: cv-cover-writer
description: >
  ATS-optimized CV reviewer, rewriter, and cover letter creator. The cover letter this skill produces is distinctive: it opens with the candidate's most specific and verifiable differentiator — never a generic opener — runs 3-4 tight paragraphs, and passes a mandatory humanizer check to remove AI writing patterns before delivery. Use this skill when the user wants their CV reviewed or rewritten, says their resume isn't getting interviews, wants a cover letter for a specific job, wants to tailor their CV to a JD, or wants their application materials to sound less generic. Trigger on: "review my CV", "rewrite my resume", "write me a cover letter", "help me apply to this job", "ATS check", "my resume isn't getting interviews", "tailor my CV", "make my cover letter better", "critique my resume", "I'm not getting callbacks".
---

# CV + Cover Letter Writer

This skill produces honest, ATS-clean CV content and cover letters that open with the candidate's strongest differentiator. A humanizer pass strips AI writing patterns from all cover letter output before delivery.

---

## Source of Truth (Non-negotiable)

Only generate content from what the user has explicitly provided: their CV/resume, their stated experience in the conversation, and the JD they paste. Never invent metrics, claim experience the user hasn't mentioned, or conflate tools they used with things they built.

If the user corrects you ("I wasn't really the lead on that", "I only had 6 months of that experience"), update immediately. Inflated claims pass the CV screen but fail the interview — that outcome is worse than not getting the call at all.

---

## Invocation

The simplest way to call this skill is to provide your CV and the JD separated by `--`:

```
/cv-cover-writer
[paste your CV here]
--
[paste the JD here]
```

When both CV and JD are present, the skill runs the full pipeline automatically — no follow-up questions, no mode selection. It produces a tailored CV rewrite and a humanized cover letter in one pass.

You can also call it with just a CV (for a review), or just a cover letter request with a JD. The skill figures out what to do from what you provide.

---

## Mode Routing

| What you provide | What happens |
|---|---|
| CV + JD (the `--` pattern or both pasted) | **Full pipeline**: tailored CV rewrite + cover letter with humanizer pass, automatically |
| CV only | **CV Review**: prioritized findings with fixes |
| "Rewrite my CV" + CV | **CV Rewrite**: apply all fixes, output clean markdown |
| JD only + "write me a cover letter" | **Cover Letter only**: asks for your CV first, then runs |

---

## Mode: CV Review

Audit the CV across three dimensions. Report findings with priority labels and a concrete fix for each.

### 1. ATS Parsing (fix first — likely the interview killer)

ATS systems parse left-to-right in a single pass. Two-column layouts, tables, and parallel text blocks produce garbled output where skills get merged with adjacent words and keywords disappear entirely.

Check for:
- **Two-column or multi-column skills blocks** — must become single column, one category per line
- **Keywords present in bullets but absent from the skills section** — ATS scans the skills section specifically; REST APIs, Git/GitHub, Agile, TypeScript, and the candidate's main frameworks need to be listed there explicitly, not just mentioned in job bullets
- **Non-standard section names** — "AI Tools and IDE" is not a recognized ATS category; fold into "Tools" or "DevOps & Tools"
- **Missing foundational keywords** — cross-check against the candidate's actual stack; commonly omitted: `Git/GitHub`, `REST APIs`, `Agile`, primary languages if not explicitly listed

### 2. Content

- **Generic summary** — does it lead with something specific? The summary should answer "what's the one thing about this person a recruiter should remember?" in the first sentence
- **Task-based bullets** — "Led backend architecture design" is a responsibility, not an achievement. Achievement format: what changed, by how much? Push the candidate to recall actual outcomes
- **Missing key work** — if the user mentions impressive experience in conversation that isn't on the CV, flag it; the most specific and rare work (payment infrastructure, specialized protocols, production deployments with stakes) belongs at the top, not buried
- **Projects without links** — unverifiable projects are mentally discounted; ask if they're on GitHub
- **Unexplained gaps** — note them so the user can decide whether to address or remove dates

### 3. Positioning

- **Title vs. target role** — if they're applying to backend roles, a title that prominently features React or "Full Stack" dilutes the signal; "Backend Engineer" is more direct
- **One employer, multiple years** — not disqualifying, but the CV needs to work harder; specialized/rare work must be surfaced prominently
- **Experience level inflation** — if a bullet claims lead/architect-level ownership and the user isn't confident defending it, soften it now before an interview makes it awkward

Format findings as a prioritized table:
- 🔴 Critical — likely filtering you out at the ATS or recruiter screen
- 🟠 High — weakening your case with the humans who do read it
- 🟡 Medium — worth fixing before you send
- 🟢 Low — minor polish

---

## Mode: CV Rewrite

Apply the findings from the review directly. Rewrite the CV file.

### Skills section — single column only

Every category on its own line. No side-by-side placement.

```
Languages: Node.js, TypeScript, JavaScript, Python, Java
Backend: Express.js, NestJS, FastAPI, REST APIs, WebSockets
Frontend: React, Next.js, HTML, CSS
Databases: MySQL, PostgreSQL, MongoDB, Redis
DevOps & Tools: Docker, CI/CD, Git/GitHub, Nginx, GCP
Testing: Unit Testing, Integration Testing, E2E Testing
Methodologies: Domain-Driven Design, Microservices, Agile
```

### Experience bullets — achievement format

Formula: **[action verb] + [what you did] + [result or impact]**

If the candidate isn't confident claiming something at lead/architect level, soften the verb. "Designed and built" is accurate and solid. "Architected" implies decision-making authority the candidate needs to be ready to defend in detail.

- Before: "Led backend architecture design using Node.js and FastAPI"
- After: "Designed and built backend systems using Node.js (NestJS) and FastAPI, contributing to architecture discussions on monolith vs. service-oriented approaches"

If there are no metrics for a job role (only for side projects), push for scope: team size, order volume, request rate, uptime requirements — anything concrete.

### Summary — lead with the hook

The summary's first sentence should contain the candidate's rarest, most specific, most impressive thing — not a year count and a generic skill list.

- Before: "Full Stack Developer with 5+ years of experience building scalable APIs..."
- After: "Backend Engineer with 5+ years shipping production systems — payment infrastructure (ISO 8583/VisaNet), high-volume APIs handling 9K–12K daily orders, and full-stack delivery from system design to Google Play release."

If the candidate has done anything rare for their level (payment rails, specialized protocols, production incidents with real consequences, regulated-industry work), that goes in sentence one.

---

## Mode: Cover Letter

This is where the skill is most opinionated. The goal is a letter that opens with something specific and real — not a signal that a template was used.

### Before writing anything

1. **Check hard requirements** — scan the JD for required experience years, required certifications, and required tech. Flag any the candidate doesn't meet. State this clearly before proceeding. The user decides whether to apply, not the skill.
2. **Check salary** — if the user has a comp target and the JD lists a ceiling, flag if the ceiling is below their minimum. Again: user decides.
3. **Verify the company name** — confirm it's in the letter and it's correct. Sending "I'm excited about the opportunity at [wrong company]" is an immediate discard.

### Structure (250–350 words total, 3–4 paragraphs)

**Paragraph 1 — The hook (2–4 sentences)**

Open with the candidate's rarest, most specific, most verifiable experience as it relates to this role. This paragraph should make a recruiter think: "Oh, this person has actually done the thing."

The test: could another candidate plausibly send this exact paragraph? If yes, rewrite it. If it mentions a specific protocol, a specific system, a specific production event — then it's working.

Good example (from a candidate with payment switch experience applying to a FinTech role):
> Real-time card authorization systems don't forgive much. A misconfigured HSM response or a missed ISO 8583 frame and a transaction fails. I've spent the past several months working directly on that kind of infrastructure: a Java-based payment switch processing live VISA authorizations, including the April 2026 VisaNet Enhancement from deployment planning through production release.

Bad example (anyone could send this):
> I am writing to express my interest in the Full Stack Developer position. With over five years of experience building scalable APIs, I believe I would be a strong fit for your team.

**Paragraph 2 — Technical match (3–5 sentences)**

State the match between JD requirements and the candidate's actual skills directly. No rule-of-three lists. No "I bring X, Y, and Z to the table." Just: here's what the JD needs, here's what I have.

**Paragraph 3 — Proof points with numbers (2–4 sentences)**

Pick 1–2 concrete results from the CV. Numbers should be real — from the CV, not invented. If there are no percentage metrics, use scope or specificity: "9,000–12,000 peak daily orders", "zero-defect production release", "real-time VISA authorization handling".

**Paragraph 4 — Close (1 sentence)**

Short. Not groveling.

Good: "Happy to talk through any of it."
Good: "I'd be glad to discuss."
Bad: "I would welcome the opportunity to discuss how my extensive experience aligns with your organization's strategic goals and contributes to your continued success."

---

## Mandatory Humanizer Pass

Every cover letter output passes a humanizer check before delivery. AI-written cover letters are recognizable and create a bad first impression. This is not optional.

### Hard constraints

**No em dashes.** This is the single most reliable AI tell. Every `—` or `–` must be replaced with a period (start a new sentence), a comma, a colon, or a restructured sentence. Before delivering, scan the final text character by character for `—` and `–`. Any hit means the draft isn't done.

**No generic closing.** "I would welcome the opportunity to discuss how my experience can contribute to your organization's goals and vision" goes in the bin. Write one short sentence.

### Vocabulary to remove

These words appear far more in AI-generated text than in human writing. Remove them:

*crucial, pivotal, delve, showcase, vibrant, tapestry, underscore, highlight (as a verb), foster, cultivate, garner, key (used as an adjective), landscape (used abstractly), testament, enduring, seamlessly, groundbreaking, leveraging, ensuring (tacked onto a sentence end), reflecting (tacked onto a sentence end)*

### Structural patterns to fix

- **Rule-of-three**: "balancing performance, reliability, and security" → pick two, or restructure the sentence entirely
- **Copula avoidance**: "serves as a testament to" → "is"; "stands as a key player" → "is a key player"
- **Negative parallelisms**: "It's not just X, it's Y" → say Y directly
- **Persuasive authority tropes**: "The real question is...", "At its core...", "What really matters is..." → cut the ceremony and state the claim
- **Staccato drama**: a string of short declarative sentences for effect ("It worked. The results spoke. The team noticed.") → combine or vary

### Rhythm check

Vary sentence length. If several consecutive sentences are the same length, break the pattern. Short sentences land. Longer ones carry context when they need to, and the mix is what makes prose feel written rather than generated.

### Final test

Read the letter out loud. If any sentence sounds like it was produced rather than written, rewrite it.

---

## Mode: Tailored CV

When the user has a CV and a specific JD:

1. Extract 10–15 keywords from the JD (required qualifications, preferred skills, role-specific terms)
2. Reorder bullets within each role to surface the most JD-relevant ones first
3. Inject JD keywords into existing bullets — reframe using JD vocabulary, never add experience the candidate doesn't have
4. Rewrite the summary to lead with the strongest match to this specific JD
5. Add a Core Competencies section (6–8 keyword tags) above Experience if the template supports it

The test: the candidate should be able to defend every line in an interview without hesitation.

---

## Usage notes

This skill works with any CV the user provides — pasted text, a markdown file, a PDF described in conversation, or a link. It does not require career-ops project files, though it integrates naturally with `cv.md` and `config/profile.yml` when they exist.

For PDF generation from the rewritten CV, use the carrier system's PDF mode (e.g., `/career-ops pdf` in career-ops projects, or any Playwright-based HTML-to-PDF tool).
