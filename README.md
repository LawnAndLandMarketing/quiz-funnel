# L&L Growth Path Quiz Funnel

**Live Quiz:** https://ll-quiz-funnel.vercel.app
**Preview All Results:** https://ll-quiz-preview.vercel.app
**Flow Diagram:** https://ll-quiz-flowchart.vercel.app
**Vercel Project:** `ll-quiz-funnel` (`prj_lTgU2rBSu8KIhyBWF8pNOoX9bM85`)
**Stack:** Pure HTML/CSS/JS — zero dependencies, zero build step
**Version:** v3 — Full Sprint 1 complete (March 21, 2026)

---

## Project Files

| File | Purpose |
|------|---------|
| `index.html` | The live quiz — 8 questions, lead capture, result routing |
| `preview.html` | Internal review page — all 5 result pages with tab navigation |
| `flowchart.html` | Visual logic map — routing, blockers, sprint status |
| `ll-quiz-flowchart.drawio` | Editable diagram — import to diagrams.net, Miro, or FigJam |

---

## Program Definitions

| Program | Monthly | Setup | Target Profile | CTA |
|---------|---------|-------|----------------|-----|
| **Authority** | $2,400/mo | $2,000 | $750K+ revenue, established operators | Book a Free Strategy Call → `/grow` |
| **Growth** | $1,200/mo | $1,500 | $200K–$750K revenue, need digital foundation | Book a Free Strategy Call → `/grow` |
| **Seed** | $697/mo | $997 | $100K–$200K revenue, solo/1-crew | Join Waitlist → `/seed-waitlist` |
| **Not a Fit** | — | — | Wrong industry / under $100K / won't invest | See below |

### Authority — $2,400/mo + $2,000 Setup
The last marketing program you'll ever need. Built to take established landscape companies from 7 to 8 figures through total market omnipresence.
- Meta & Google Ads management — full campaign execution across every paid channel
- SEO that compounds — organic visibility that grows month over month
- AI-powered lead automations — follow up in under 2 minutes, 24/7
- High-converting landing pages — built for your market and your services
- Full CRM + pipeline automation via Service Area Expert
- Dedicated account manager — one person who knows your business
- Omnipresent local visibility — when they search anything related to what you do, you show up

### Growth — $1,200/mo + $1,500 Setup
For operators who've proven the business works but need a professional digital foundation. Clear upgrade path to Authority at $750K.
- Custom lead-generating website
- CRM setup + lead tracking via Service Area Expert
- GBP optimization + local SEO
- Review generation system
- Monthly reporting + strategy calls

### Seed — $697/mo + $997 Setup
Entry-level, self-serve model. Waitlist-controlled for quality assurance.
- AI-built professional website + hosting + maintenance
- Service Area Expert CRM provisioned and configured
- Automated GBP posting (AI-generated, scheduled)
- LSA setup guidance + review request sequences
- Self-serve knowledge base + community access
- **No dedicated AM** — ticket-based support only
- **Graduation trigger:** $250K annual revenue run rate → Growth consultation call

### Not a Fit — 3 Variants

| Variant | Trigger | CTA |
|---------|---------|-----|
| **Wrong Industry** | Q1 = No | "Learn More About What We Do" + "← Go back (made a mistake?)" |
| **Too Early** | Q3 revenue < $100K | "Get the Free GBP Checklist →" `/checklist-download/` |
| **Won't Invest** | Q8 = "can't commit" | "Book a Free Strategy Call →" `/grow` + note below button |

Not-a-fit paths skip the lead capture form entirely.

---

## Routing & Scoring Logic

### The Hierarchy (Revenue-First)

| Priority | Signal | Role |
|----------|--------|------|
| Gate | Q1: Industry | Hard disqualifier — not green industry = immediate exit (wrong-industry variant) |
| 1st | Q3: Revenue | **Primary gate** — $750K+ → Authority · $200K–$750K → Growth · $100K–$200K → Seed · Under $100K → Not a Fit (too-early variant) |
| 2nd | Q2: Crews | Supporting signal only — reinforces revenue, never overrides it |
| Floor | Q8: Investment | "Can't commit" = Not a Fit (invest variant). Otherwise does not change tier. |
| Context | Q4–Q7 | Shape result page copy + personalization only. Do NOT affect routing. |

### Edge Case Routing

| Scenario | Signals | Routes To | Rationale |
|----------|---------|-----------|-----------|
| Big company, small budget | 5 crews, $1.2M rev, low budget | **Authority** | Revenue wins. Budget is a sales conversation. |
| Small crew, high budget | 2 crews, $400K rev, high budget | **Growth** | Revenue wins. High budget doesn't upgrade tier. Result notes graduation path. |
| Solo op, high revenue | 1 crew, $800K rev | **Authority** | Revenue wins. Capacity note in personalization. |
| 3+ crews under $750K | 3 crews, $500K rev | **Growth** | Revenue under $750K. Crews don't override. |
| High revenue, never ran ads | $1M+ rev, no ad exp | **Authority** | Revenue wins. Personalized copy: accelerator framing, not gamble. |

### Scoring Code

```js
function computeResult() {
  if (answers[8]?.score === 'notfit_invest') return 'notfit-invest';
  if (answers[3]) {
    const rev = answers[3].score;
    if (rev === 'notfit_revenue') return 'notfit-early';
    if (rev === 'seed') return computeWithRevenue('seed');
    if (rev === 'growth') return computeWithRevenue('growth');
    if (rev === 'authority') return computeWithRevenue('authority');
  }
  return majorityVote();
}
function computeWithRevenue(revTier) {
  const crews = answers[2]?.score;
  if (revTier === 'authority' && crews === 'seed') return 'authority'; // solo + high rev
  if (revTier === 'growth' && crews === 'authority') return 'growth';  // 3+ crews + <$750K
  return revTier;
}
```

---

## The 8 Questions

| # | Question | Role |
|---|----------|------|
| **Q1** | Green industry services? | Hard gate — No = immediate wrong-industry exit |
| **Q2** | How many trucks/crews? | Low-friction opener. Supporting signal only. |
| **Q3** | Annual revenue? | **Primary routing gate.** Tier boundaries: $100K / $200K / $750K |
| **Q4** | #1 growth goal? | Aspirational. Informs result page copy only. |
| **Q5** | Current website situation? | Digital maturity context. Copy only. |
| **Q6** | Paid ads experience? | Copy context. Never-ran-ads + high rev = personalized Authority copy. |
| **Q7** | Biggest marketing struggle? | Pain point — echoed on result page. |
| **Q8** | Monthly investment commitment? | Hormozi framing — mindset gate, not budget gate. "Can't commit" = Not a Fit. |

---

## Lead Capture + N8N Webhook (Sprint 1 ✅)

**Capture form** shown after Q8 for Authority / Growth / Seed only (not-a-fit skips it).
Fields: First Name, Last Name, Email, Phone, Company Name

**N8N Workflow:** `[KAI] Quiz Funnel — Lead Capture` (ID: `FFNNEzoY5KevT3v9`)
**Webhook URL:** `https://lawnandlandmarketing.app.n8n.cloud/webhook/ll-quiz-lead-capture`

**Payload sent on every submission:**
```json
{
  "firstName": "", "lastName": "", "email": "", "phone": "", "company": "",
  "result": "authority | growth | seed | notfit-early | notfit-invest | notfit-industry",
  "answers": { "q1_industry": "", "q2_crews": "", "q3_revenue": "", ... },
  "signals": { "revenueScore": "", "crewScore": "", "investScore": "", "goalMeta": "", "struggleMeta": "", "adsMeta": "" },
  "source": "ll-quiz-funnel",
  "submittedAt": "ISO timestamp",
  "pageUrl": ""
}
```

**SAE Routing:**
- `quiz-authority` → Authority sequence
- `quiz-growth` → Growth sequence
- `quiz-seed` → Seed sequence
- SAE Location: 🌿 Lawn & Land Marketing (`x5MOOAaH49HyBUXG1NNt`)
- Credential in N8N: `GHL L&L Sub-Account API` (`IlCZXnqnjF9Y9IPu`)

**⚠️ Still needed:** SAE follow-up sequences (one per program) triggered by these tags.

---

## Follow-Up Sequences (Built — SAE Implementation Pending)

Full sequences emailed to matt@lawnandlandmarketing.com on March 21, 2026.

| Program | Touchpoints | Goal | Core Angle |
|---------|-------------|------|------------|
| Authority | 6 (3 SMS + 3 email) over 7 days | Strategy call booked | Omnipresence, 7→8 figures, last program you'll ever need |
| Growth | 6 (3 SMS + 3 email) over 7 days | Apply or book call | Foundation first, predictable leads, clear graduation path |
| Seed | 4 (2 SMS + 2 email) over 5 days | Waitlist signup | Quality-controlled onboarding, full info sent on join |

---

## Sprint Roadmap

### ✅ Sprint 1 — Lead Capture + N8N Webhook
- Form captures all lead data before revealing results
- N8N workflow live, routes to SAE by program tag
- ⚠️ **Pending:** SAE follow-up sequences (Authority / Growth / Seed)

### ✅ Sprint 2 — Revenue-First Routing
- Revenue-first hierarchy replaces equal-weight majority vote
- Q1 industry gate (immediate wrong-industry exit)
- All 5 edge cases wired
- 3 distinct Not-a-Fit result pages
- Personalization callouts on Authority + Growth

### ⏳ Sprint 3 — Conversion Optimization
- Per-question drop-off tracking (analytics)
- Breather/social proof slide after Q4
- Urgency element before capture form

### ⏳ Sprint 4 — Meta Retargeting (requires Sprint 1 SAE tags first)
- Meta Custom Audiences segmented by Q4 (goal) + Q7 (struggle) from SAE tags
- "Didn't finish quiz" retargeting audience

---

## Open Items

- [ ] Build SAE follow-up sequences (Authority / Growth / Seed) triggered by quiz tags
- [ ] Real CTA URLs — apply page, seed waitlist page at lawnandlandmarketing.com/seed-waitlist
- [ ] Real client testimonials for loading screen (currently placeholder: Chris M., Olson Outdoors)
- [ ] Seed: GBP automated posting tech stack (N8N/GHL feasibility)
- [ ] Seed: community format — Facebook group vs. SAE channel
- [ ] Seed: monthly AI performance snapshot email (V2 consideration)

---

## Result Pages — Content Summary

### Authority
- **Badge:** ⚡ Your Match
- **Heading:** You're Ready for the Authority Program
- **Tagline:** "The last marketing program you'll ever need — built to take established landscape companies from 7 to 8 figures through total market omnipresence."
- **Includes label:** The complete system
- **Next Step:** Discovery call → deeper dive if fit
- **CTA:** Book a Free Strategy Call → lawnandlandmarketing.com/grow
- **Footer:** 🌴 Green Industry Experts · ⭐️ 98% Client Retention · 📈 Your Dedicated Growth Partner

### Growth
- **Badge:** 🚀 Your Match
- **Heading:** You're Ready for the Growth Program
- **Tagline:** Foundation-first, predictable leads, clear upgrade path to Authority at $750K.
- **Next Step:** Discovery call → deeper dive if fit
- **CTA:** Book a Free Strategy Call → lawnandlandmarketing.com/grow
- **Footer:** 🌴 Green Industry Experts · ⭐️ 98% Client Retention · 📈 Your Dedicated Growth Partner

### Seed
- **Badge:** 🌱 Your Match
- **Heading:** Start Strong with the Seed Program
- **Tagline:** Digital foundation for operators building toward consistent leads.
- **Next Step:** Waitlist = quality-controlled onboarding, full info sent on join, hit the ground running on day one.
- **CTA:** Join the Seed Waitlist → lawnandlandmarketing.com/seed-waitlist
- **Footer:** 🌴 Green Industry Experts · ⭐️ 98% Client Retention · 📈 Your Dedicated Growth Partner

### Not a Fit — Too Early (under $100K)
- **Badge:** 💡 Honest Take
- **Heading:** Let's Set You Up for Success First
- **3 free moves:** GBP optimization, 10 Google reviews, LSA application
- **CTA:** Get the Free GBP Checklist → lawnandlandmarketing.com/checklist-download/

### Not a Fit — Won't Invest (has revenue, won't commit)
- **Badge:** 💡 Honest Take
- **Heading:** The Business Is Ready. The Timing Might Not Be.
- **Tips:** Referral system, GBP, know your numbers
- **CTA:** Book a Free Strategy Call → lawnandlandmarketing.com/grow
- **Sub-note:** "Curious about our programs? We'd love to hear about your business — no pitch, just a conversation."

### Not a Fit — Wrong Industry
- **Badge:** Not a Match
- **Heading:** We Exclusively Serve the Green Industry
- **CTA:** Learn More About What We Do → lawnandlandmarketing.com
- **Ghost link:** ← Go back (made a mistake?)

---

## Design System

L&L Digital UI Standard:
- **Background:** `#040a04` · **Accents:** `#ACE71D` (lime) + `#5DCA49` (green)
- **Depth:** SVG grain (3.5% opacity) + radial ambient glow
- **Cards:** Glass morphism — `rgba(255,255,255,0.04)` + `backdrop-filter: blur(12px)`
- **Typography:** Rethink Sans 800 Italic (headings) + Inter (body)

---

## Deployment

```bash
VERCEL_TOKEN="your_token"
TEAM_ID="team_sPQaPdEOVQaR42djHV2cTm51"
HTML_B64=$(base64 -i index.html | tr -d '\n')
curl -X POST "https://api.vercel.com/v13/deployments?teamId=${TEAM_ID}" \
  -H "Authorization: Bearer ${VERCEL_TOKEN}" \
  -H "Content-Type: application/json" \
  -d "{\"name\":\"ll-quiz-funnel\",\"target\":\"production\",\"files\":[{\"file\":\"index.html\",\"data\":\"${HTML_B64}\",\"encoding\":\"base64\"}],\"projectSettings\":{\"framework\":null}}"
```

---

## Context for Claude (when building out)

**What Lawn & Land Marketing does:**
Digital marketing agency exclusively for lawn & landscape companies ($750K–$9M revenue). ~54 clients, ~$88K MRR, 98% retention, $3M+ ad spend managed (95% Meta). Green industry only.

**Positioning:**
- Authority = omnipresence system, 7→8 figures, the whole board (ads + SEO + AI + automations + website + CRM)
- Growth = digital foundation → consistent leads → clear path to Authority
- Seed = self-serve digital foundation, quality-controlled waitlist, graduates at $250K run rate

**Quiz psychology:**
1. Industry gate (Q1) — instant exit for wrong audience
2. Low-friction opener (Q2 crews) — starts yes-chain
3. Revenue gate (Q3) — primary qualifier, sets tier
4. Aspirational (Q4) — hits harder when invested
5. Loading screen = captive attention — best testimonial
6. "The Next Step" pill badge — visually breaks the wall of text before CTA
7. Strategy call framing = discovery only, no pitch — deeper dive scheduled if fit

**Brand voice:** Direct, confident, green industry native. No fluff. "Leads, jobs booked, ROI" language only.
