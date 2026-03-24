# L&L Growth Path Quiz Funnel

**Live Quiz:** https://ll-quiz-funnel.vercel.app
**Preview All Results:** https://ll-quiz-preview.vercel.app
**Flow Diagram:** https://ll-quiz-flowchart.vercel.app/flowchart.html
**Analytics Dashboard:** https://ll-quiz-analytics.vercel.app
**Vercel Project:** `ll-quiz-funnel` (`prj_lTgU2rBSu8KIhyBWF8pNOoX9bM85`)
**Stack:** Pure HTML/CSS/JS — zero dependencies, zero build step
**Version:** v4 — GA4 tracking + analytics dashboard (March 24, 2026)

---

## Project Files

| File | Purpose |
|------|---------|
| `index.html` | The live quiz — 8 questions, lead capture, result routing, GA4 + Meta Pixel tracking |
| `preview.html` | Internal review page — all 6 result pages with tab navigation |
| `flowchart.html` | Visual logic map — routing, blockers, sprint status |
| `ll-quiz-flowchart.drawio` | Editable diagram — import to diagrams.net, Miro, or FigJam |

---

## Analytics & Tracking

### GA4 Setup

| Property | Measurement ID | Property ID |
|----------|---------------|-------------|
| L&L Quiz Funnel | `G-KVF41T7117` | `529853510` |

**GA4 events fired by the quiz:**

| Event | Trigger | Parameters |
|-------|---------|-----------|
| `quiz_start` | Quiz begins (start button clicked) | `referrer` |
| `quiz_step_view` | Every question viewed | `step_number`, `step_name`, `time_on_prev_step_sec` |
| `quiz_funnel_step_[name]` | Each step — one named event per step | `step_number`, `step_name` |
| `quiz_answer` | Every answer selection | `step_number`, `step_name`, `answer_label`, `answer_score` |
| `quiz_result` | Result page shown | `result_type`, `total_time_sec` |
| `quiz_lead_captured` | Form submitted | `result` + `generate_lead` with dollar value |
| `quiz_abandon` | Tab closed/hidden mid-quiz | `last_step`, `last_step_name`, `answers_count`, `time_since_start_sec` |

**Funnel step names (in order):**
`quiz_start` → `industry_gate` → `crews` → `revenue` → `goal` → `website` → `ads` → `struggle` → `investment` → `lead_capture` → `quiz_lead_captured`

**Meta Pixel:** `235509009483991` — fires PageView, ViewContent (result shown), Lead (form submit), InitiateCheckout (seed waitlist click, $697 value)

### Analytics Dashboard

**URL:** https://ll-quiz-analytics.vercel.app
**Vercel Project:** `quiz-analytics` (`prj_KtLyzWGq5nWku9krUYnFiRLmQoPj`)
**Stack:** Vanilla HTML/CSS/JS + Vercel Serverless Function (Node.js) → GA4 Data API

**Dashboard shows:**
- KPIs: quiz starts, completions, completion rate, leads captured, lead capture rate, avg time to complete
- Drop-off funnel: step-by-step with % remaining and drop-off %
- Result distribution: Authority / Growth / Seed / Not-a-Fit counts and %
- Top exit points (steps with highest abandonment)
- Sessions by day: starts, completions, lead captured, traffic source

**Date ranges:** 7 days / 14 days / 30 days / 90 days / All time (from launch date Mar 24, 2026)
**Auto-refresh:** every 5 minutes

**Authentication:**
- Google Cloud Project: `quiz-which-program`
- Service Account: `ll-quiz-analytics@quiz-which-program.iam.gserviceaccount.com`
- GA4 role: Viewer on property `529853510`
- Vercel env vars: `GA4_PROPERTY_ID` + `GOOGLE_SA_KEY` (encrypted)

### ⚠️ Pending: Register Custom Dimensions in GA4

The `result_type` parameter (authority/growth/seed/notfit) is being passed with `quiz_result` events but **cannot be queried via the Data API until it's registered as a custom dimension in GA4.**

**To enable full result breakdown in the analytics dashboard:**
1. Go to **analytics.google.com → Admin → Custom definitions → Custom dimensions**
2. Click **Create custom dimensions**
3. Dimension name: `result_type`
4. Scope: **Event**
5. Event parameter: `result_type`
6. Click **Save**

Once registered, wait 24–48h for data to populate, then update `/api/ga4.js` in the `getResults()` function to use `{ name: 'customEvent:result_type' }` as a dimension in the `quiz_result` report. This will unlock the Authority/Growth/Seed/Not-a-Fit breakdown chart in the dashboard.

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

## Lead Capture + N8N Webhook

**Capture form** shown after Q8 for Authority / Growth / Seed only (not-a-fit skips it).
Fields: First Name, Last Name, Email, Phone, Company Name

**N8N Workflow:** `[KAI] Quiz Funnel — Lead Capture` (ID: `FFNNEzoY5KevT3v9`)
**Webhook URL:** `https://lawnandlandmarketing.app.n8n.cloud/webhook/ll-quiz-lead-capture`

**Payload sent on every submission:**
```json
{
  "firstName": "", "lastName": "", "email": "", "phone": "", "company": "",
  "result": "authority | growth | seed | notfit-early | notfit-invest | notfit-industry",
  "answers": { "q1_industry": "", "q2_crews": "", "q3_revenue": "", "..." },
  "signals": { "revenueScore": "", "crewScore": "", "investScore": "", "goalMeta": "", "struggleMeta": "", "adsMeta": "" },
  "source": "ll-quiz-funnel",
  "submittedAt": "ISO timestamp",
  "pageUrl": ""
}
```

**SAE Routing:**
- `quiz-authority` → Authority follow-up sequence (6 steps / 7 days)
- `quiz-growth` → Growth follow-up sequence (6 steps / 7 days)
- `quiz-seed` → Seed follow-up sequence (4 steps / 5 days)
- SAE Location: 🌿 Lawn & Land Marketing (`x5MOOAaH49HyBUXG1NNt`)

**9 SAE Custom Fields (all set on contact upsert):**
`quiz_result`, `quiz_revenue_tier`, `quiz_crew_count`, `quiz_investment_tier`, `quiz_growth_goal`, `quiz_biggest_struggle`, `quiz_ads_experience`, `quiz_website_situation`, `quiz_submitted_at`

---

## Meta Ads Campaign

**Campaign:** Quiz Funnel - Website Leads - Mar 2026 (ID: `120241640444540175`) — **PAUSED, ready to launch**
**Ad Set:** Quiz Funnel - Broad - Website (ID: `120241640446140175`)
- US, 18–65, broad, $350/day, OFFSITE_CONVERSIONS, pixel Lead event
- Destination: https://ll-quiz-funnel.vercel.app

**5 Quiz Ads (all PAUSED):**
| Ad | Hook |
|----|------|
| QZ#01 — Which Program Actually Fits? | Template vs. fit |
| QZ#02 — Stop Guessing, Start Knowing | SEO vs. ads confusion |
| QZ#03 — The Race to the Bottom | Angi/shared leads pain |
| QZ#04 — AI Won't Tell You This | AI tool vs. strategy |
| QZ#05 — 2 Minutes to Clarity | 97% retention proof |

To launch: **unpause the campaign.** Existing lead form campaign stays untouched (different objective — `QUALITY_LEAD` on-Facebook vs. `OFFSITE_CONVERSIONS` website).

---

## Sprint Status

| Sprint | Status | Description |
|--------|--------|-------------|
| Sprint 1 | ✅ Complete | Lead capture + N8N webhook + SAE follow-up sequences |
| Sprint 2 | ✅ Complete | Revenue-first routing, all result pages, edge cases |
| Sprint 3 | ✅ Complete | Drop-off tracking (N8N), Meta Pixel, seed waitlist pre-fill |
| Sprint 4 | ⏳ Pending | Meta retargeting audiences from SAE quiz tags |

### Sprint 4 — Meta Retargeting
Create Meta Custom Audiences from SAE tags:
- `quiz-authority` → Authority retargeting
- `quiz-growth` → Growth retargeting
- `quiz-seed` → Seed retargeting
- Segment further by `quiz_biggest_struggle` and `quiz_growth_goal` custom fields

---

## Open Items

| Item | Priority | Owner |
|------|----------|-------|
| Register `result_type` custom dimension in GA4 | High | Matt (2 min task — see Analytics section above) |
| Launch quiz Meta campaign | When ready | Matt (unpause campaign ID `120241640444540175`) |
| Sprint 4: Meta retargeting audiences from SAE tags | Medium | Kai |
| CAPI (Conversion API) server-side backup for pixel | Medium | Kai |
| Real CTA URLs — apply page, seed waitlist page | Low | Matt |
| Seed: GBP automated posting tech stack | Low | Kai |
| Seed: community format decision | Low | Matt |

---

## Deployment

```bash
# Deploy index.html to ll-quiz-funnel Vercel project
VTOKEN="your_vercel_token"
curl -X POST "https://api.vercel.com/v13/deployments?teamId=team_sPQaPdEOVQaR42djHV2cTm51" \
  -H "Authorization: Bearer $VTOKEN" \
  -H "Content-Type: application/json" \
  -d "{\"name\":\"ll-quiz-funnel\",\"target\":\"production\",\"files\":[{\"file\":\"index.html\",\"data\":$(python3 -c "import json; print(json.dumps(open('index.html').read()))")}],\"projectSettings\":{\"framework\":null}}"
```

---

## Design System

L&L Digital UI Standard:
- **Background:** `#040a04` · **Accents:** `#ACE71D` (lime) + `#5DCA49` (green)
- **Depth:** SVG grain (3.5% opacity) + radial ambient glow
- **Cards:** Glass morphism — `rgba(255,255,255,0.04)` + `backdrop-filter: blur(12px)`
- **Typography:** Rethink Sans 800 Italic (headings) + Inter (body)

---

## Context for Future Builders

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
