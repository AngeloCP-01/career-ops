---
name: feedback-pdf-tailoring
description: How to tailor CVs without diluting them — preserve metrics and system names, rewrites must amplify not weaken
metadata:
  type: feedback
---

When tailoring `cv.md` content into a per-role PDF, rewriting bullets is allowed and often preferred — BUT only when the rewrite is objectively stronger than the original. The user explicitly rejected a prior pass where the agent merged bullets into generic JD-vocabulary and lost specificity.

**Why:** User's `cv.md` has carefully crafted bullets with concrete metrics (~35%, ~40%, ~25%, ~30%, "9,000-12,000 peak daily orders") and specific system names (ISO 8583, AS400, HSM, FEP, VisaNet, TiDB). These are the exact signals that make the CV credible to recruiters. A diluted "decomposed services using DDD" loses what "designing domain-isolated services using DDD principles, reducing cross-domain coupling and deployment risk" was telling the reader.

**How to apply:**
- Verbatim metrics ALWAYS survive any rewrite, word for word
- Specific system names survive when relevant to the role
- Concrete decisions ("balancing monolithic vs microservices trade-offs") don't get collapsed into generic verbs ("designed scalable systems")
- Merging 2-3 bullets into 1 is forbidden if it drops content
- Test: read original then rewrite. Rewrite must be clearly stronger on verb + JD-fit + concreteness, or revert to original.
- Full rules in [[modes/pdf.md]] "Estrategia de tailoring (REESCRIBE para AMPLIFICAR, nunca para DILUIR)" section.

Single-page is preferred; the template at `templates/cv-template.html` is tuned to fit full cv.md content at 9pt body / 0.35in margins.
