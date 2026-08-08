# Kisan Scheme Match — Full Project Blueprint
### An AI-assisted eligibility & discovery platform for agricultural government schemes

---
### doc file link--
[Document link](https://docs.google.com/document/d/1DAwOe3Cg1mNlP-5FvlY2PW6i0simqvrAnAxsBGGGmfQ/edit?usp=sharing)

## 1. Does this already exist? (Market check)

You should know this landscape before you pitch — judges will ask.

| Product | What it does | Gap you can fill |
|---|---|---|
| **myScheme.gov.in** (MeitY/NeGD, launched 2022) | National platform covering 4,700+ Central + State schemes across *all* sectors (not just agri). Farmer fills demographic form → rule-based eligibility filter → ranked list → application link. 15 languages. | It's **generalist**, not agri-specific. No land-holding, crop, irrigation-type, or MSP/loan-category filters. No economic-proxy scoring (income is self-declared, unverified). No admin workflow for tracking applications, no district-level scheme popularity/reporting dashboards. |
| **UMANG app** | Aggregates govt *services* (not eligibility discovery) | Transactional, not discovery-oriented |
| **Kisan Suvidha, mKisan, PM-KISAN portal, PMFBY portal** | Single-scheme portals, each with its own login/application flow | Fragmented — farmer must know the scheme name already |
| **State agri portals** (e.g., Raitha Mitra–Karnataka, e-Krishi, AgriSNet) | State-only, single-state schemes | No cross-state / cross-level (state+centre) unification |
| **JanSevaPlus and similar private "eligibility checker" blogs** | SEO wrappers around myScheme, no real backend | Not a real product |

**Your differentiation (pitch this explicitly):**
1. **Agriculture-vertical depth** — land size/type, crop, irrigation source, MSP-registration status, tenancy status — none of the general portals model this.
2. **Computed economic-proxy score** instead of self-declared income (India's own SECC-2011 methodology already uses asset/utility proxies — electricity, LPG, housing type, vehicle ownership — instead of trusting declared income, precisely because rural self-reported income is unreliable). You're modernizing that same idea into a live scoring engine.
3. **Two-sided workflow** — most tools stop at "here's a list of links." You're adding actual **application intake + admin review + status tracking**, which turns this from a search engine into a mini e-governance workflow tool.
4. **District/state "prominent schemes" analytics** — nobody in this space publishes a public dashboard of *which schemes are actually being discovered/applied for in a district*. That's a genuinely new, defensible feature (and useful to real Krishi Vigyan Kendras / Agriculture Departments).

Say this plainly in your pitch: *"myScheme solves discovery for the whole population. We solve discovery + eligibility scoring + application workflow + local analytics for one high-need vertical: agriculture."*

---

## 2. Who uses this (roles)

1. **Farmer (applicant)** — builds a profile, browses/filters schemes, sees ranked matches with match-reason highlights, saves/bookmarks, applies, tracks status.
2. **Scheme Admin (state/district officer or your hackathon "admin")** — adds/edits scheme text, reviews submitted applications, changes application status, sees local analytics.
3. **Super Admin** — manages admin accounts, scheme master data, and system-wide reports.
4. **Public visitor (no login)** — can view the public "Prominent Schemes in my District/State" page and general reports, without submitting a profile. Good for transparency and for judges to see value without needing a fake farmer login.

---

## 3. Full feature set

### 3.1 Farmer profile (this is your richest data-collection surface — go deep)

**A. Identity & location**
- Name, phone/Aadhaar-linked ID (mock for hackathon), state, district, village, category (SC/ST/OBC/General), gender, age, differently-abled status.

**B. Land & farming profile**
- Land size (acres/hectares), land ownership type (owned/leased/sharecropper — this alone changes eligibility for a lot of schemes), soil type, irrigated vs rainfed, irrigation source (borewell/canal/rain-fed/drip), primary crop(s), cropping season (Kharif/Rabi/Zaid), farm mechanization owned (tractor, thresher, etc.), livestock owned, whether registered on **e-NAM** or has an **MSP procurement ID**.

**C. Economic wealth proxy inputs (this is your standout feature)**
Instead of asking "what's your income" (unreliable, and low-income framing feels invasive), ask observable proxies — same logic as SECC-2011's deprivation-indicator approach:
- Electricity connection type & average monthly units consumed
- LPG connection (yes/no; Ujjwala beneficiary or regular)
- House type (kutcha/semi-pucca/pucca), number of rooms
- Vehicle ownership (two-wheeler / tractor / four-wheeler / none)
- Bank account + Kisan Credit Card status
- Existing loan/debt status

→ Feed these into a **weighted Economic Vulnerability Index (EVI)**, scored 0–100 (lower = more vulnerable/needs support). This becomes a real scoring feature you can show off, not just a form field.

**D. Intent filter (what the farmer is looking for)**
Multi-select chips: `Loans/Credit` · `MSP/Procurement` · `Input Subsidy (seeds/fertilizer)` · `Crop Insurance` · `Equipment/Mechanization` · `Irrigation` · `Organic/Sustainable farming` · `Storage/Warehousing` · `Market linkage` · `Women/SC-ST specific` · `Disaster relief` — this drives a hard pre-filter before ranking.

### 3.2 Scheme repository (admin-fed)
- Scheme name, issuing level (Centre/State), issuing state (if state scheme), department, scheme category (matches the intent chips above), free-text description (eligibility criteria, benefits, documents required — pasted as plain text, no structured parsing needed, per your PS), application link, deadline (if any), status (active/closed/upcoming).

### 3.3 Matching engine (core of the PS — see §5 for detail)
- Hard filters first (state/centre applicability, category exclusivity, land-size thresholds if the scheme text states them, scheme-type intent).
- Then **TF-IDF + cosine similarity** ranks the *remaining* schemes against a profile-derived query document.
- Highlight matched vs missing eligibility keywords.
- EVI score used as an additional soft-boost for schemes tagged "for economically weaker sections."

### 3.4 Farmer Dashboard
- Ranked scheme list with match %, matched/missing keyword chips, "Save," "Apply," scheme-type filter chips, state/centre toggle.
- "My Applications" tab — status tracker (Submitted → Under Review → Approved/Rejected).
- "Prominent Schemes near me" widget (top schemes by applications in their district/state).

### 3.5 Scheme Status / Details Page
- Full scheme text, eligibility summary, required documents, official application link, deadline, and (once you have data) "X farmers in your district have applied."

### 3.6 Admin Portal
- **Authentication/authorization** (role-based: Super Admin / District Admin) — build this properly, it's a real feature judges will check.
- Scheme CRUD (add/edit/paste text/upload).
- **Application inbox**: list of farmer applications, filter by scheme/district/status, view full farmer profile + matched scheme, change status, add remarks.
- **District/State report page**: most-applied schemes, EVI distribution of applicants, scheme-category demand breakdown, unmatched/underserved farmer segments (e.g., "40% of profiles in District X don't match any active scheme in category Loans — signal a gap").

### 3.7 Public Report Page
- Same analytics as admin but anonymized/aggregated — no individual farmer data. This is your "public good" story for the pitch: transparency for citizens and policymakers.

### 3.8 Bookmark/save
- Simple saved-list per farmer, persisted to DB (not just localStorage, since you want it to survive login sessions).

---

## 4. Additional features worth adding (stretch goals, ranked by ROI for a hackathon)

**High ROI / low effort:**
- **Eligibility gap explainer**: "You match 6/9 keywords for PM-KISAN; missing: 'landholding under 2 hectares'" — you already planned this, just make it prominent, it's a big demo wow-factor.
- **Multilingual UI** (even just Hindi + English toggle) — huge credibility boost for an agri tool.
- **SMS/WhatsApp-style notification mock** when a new matching scheme is added or deadline approaches.
- **Document checklist generator** per scheme (extract "documents required" section via keyword rules — Aadhaar, land record/7-12 extract, bank passbook, etc.)

**Medium ROI:**
- **Geo/choropleth map** of scheme demand by district (state map, shaded by application volume) — visually strong for judging.
- **Chatbot-style Q&A** over scheme text (simple keyword-retrieval, not full RAG needed) — "Ask about a scheme" box.
- **Scheme comparison table** (pick 2–3 schemes side by side).
- **Feedback loop**: farmer marks "I applied and got rejected — reason?" to eventually recalibrate scoring.

**Ambitious (only if time permits):**
- **Offline-first PWA** with local caching for low-connectivity rural use — genuinely important in the real world and a strong talking point even if only partially implemented.
- **Voice input for profile form** (many small farmers are low-literacy) — even a basic browser Web Speech API demo scores well.
- **Fraud/duplicate-application detection** using Aadhaar-hash matching (mocked).

---

## 5. Matching algorithm — how to actually build it (no embeddings needed, as your PS says)

**Step 1 — Hard filters (rule-based, before any scoring):**
- `scheme.level == Centre OR scheme.state == farmer.state`
- `scheme.category ∈ farmer.selected_intents` (if farmer picked filters)
- Category-exclusivity checks (e.g., scheme text mentions "SC/ST only" → regex/keyword check against farmer.category)
- Optional numeric threshold parsing: run a lightweight regex over scheme text for patterns like `under 2 hectares`, `land holding up to 5 acres`, `annual income below ₹2,50,000` and compare against farmer's structured fields where a match is unambiguous. (This is *not* full NLP — just regex over numbers + units + comparators. Cheap, high value, very demoable.)

**Step 2 — Build the "farmer query document":**
Concatenate all profile fields into a synthetic text blob (this is the trick for TF-IDF to work well without embeddings):
```
"small farmer 1.5 acres owned land Bihar SC category rainfed
paddy Kharif season no tractor KCC not available loan credit
input subsidy irrigation drip"
```
Include both raw values *and* semantic tags you derive (e.g., land<2ha → add token "small_marginal_farmer"; EVI<40 → add token "economically_weak"; no LPG → add token "vulnerable_household"). This tagging step is what makes TF-IDF punch above its weight — you're pre-injecting domain knowledge as vocabulary rather than relying on the vectorizer to infer it.

**Step 3 — TF-IDF + cosine similarity (scikit-learn):**
```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

corpus = [scheme.description for scheme in filtered_schemes] + [farmer_query_doc]
vectorizer = TfidfVectorizer(stop_words='english', ngram_range=(1,2))
tfidf_matrix = vectorizer.fit_transform(corpus)

scheme_vectors = tfidf_matrix[:-1]
farmer_vector = tfidf_matrix[-1]
scores = cosine_similarity(farmer_vector, scheme_vectors).flatten()
```
- Rank `filtered_schemes` by `scores` descending.
- Blend in a small EVI-based boost: `final_score = 0.85*cosine_score + 0.15*evi_alignment_score` where `evi_alignment_score` rewards schemes explicitly targeting economically weaker sections when the farmer's EVI is low.

**Step 4 — Keyword highlight (matched/missing):**
- Extract top TF-IDF terms from each scheme description (the terms with highest TF-IDF weight in that scheme's vector).
- Intersect with farmer_query_doc tokens → "matched keywords."
- Remaining top scheme terms → "missing keywords" (shown to farmer as "you may want to check: tenant farmer status, irrigation type").

This whole pipeline is standard scikit-learn, runs in milliseconds, and is very easy to explain to judges on a whiteboard — which matters as much as it working.

---

## 6. Economic Vulnerability Index (EVI) — worked formula

Score 0 (most vulnerable) to 100 (least vulnerable), inspired by SECC-2011 deprivation-indicator logic (asset/utility proxies rather than self-declared income):

| Factor | Weight | Scoring logic |
|---|---|---|
| Land size | 25% | <1 acre=0, 1–2=10, 2–5=20, >5=25 |
| House type | 15% | kutcha=0, semi-pucca=8, pucca=15 |
| Electricity units/month | 10% | 0 (no connection)→10 (>200 units) |
| LPG connection | 10% | none=0, Ujjwala=5, regular=10 |
| Vehicle ownership | 10% | none=0, two-wheeler=5, tractor/car=10 |
| Irrigation access | 15% | rainfed=0, partial=8, full irrigation=15 |
| Bank/KCC access | 15% | no account=0, account only=8, KCC active=15 |

Sum → EVI. Bucket into **Highly Vulnerable (0–35) / Moderately Vulnerable (36–65) / Stable (66–100)** and use the bucket both to boost relevant schemes and to power your admin analytics ("62% of applicants in District X fall in Highly Vulnerable band").

Be transparent about this in your pitch: it's a **proxy heuristic for prioritization/ranking, not a legal eligibility determination** — real welfare eligibility still runs through official income/asset certificates. Frame it as "decision support," not "final say." This caveat also protects you from over-claiming in front of judges.

---

## 7. System architecture

```
┌─────────────────────────┐        ┌──────────────────────────┐
│   Farmer Web/PWA Client │◄──────►│   API Gateway (FastAPI)   │
│  (React + Tailwind)     │        │                            │
└─────────────────────────┘        │  /auth  /profile  /match  │
┌─────────────────────────┐        │  /schemes /applications   │
│   Admin Portal (React)  │◄──────►│  /reports  /bookmarks     │
└─────────────────────────┘        └───────────┬───────────────┘
                                                │
                        ┌───────────────────────┼───────────────────────┐
                        │                       │                       │
               ┌────────▼────────┐   ┌──────────▼──────────┐  ┌────────▼────────┐
               │  Matching Engine │   │   PostgreSQL DB      │  │  Auth Service    │
               │ (TF-IDF+cosine,  │   │  farmers, schemes,   │  │ (JWT + RBAC)     │
               │  rule filters,   │   │  applications,       │  │                  │
               │  EVI calc)       │   │  bookmarks, audit_log│  │                  │
               └──────────────────┘   └───────────────────────┘  └──────────────────┘
```

- **Matching Engine** can be a stateless Python microservice (FastAPI + scikit-learn), called synchronously on profile submit/update, with results cached (Redis or even just a DB table) since scheme corpus changes rarely.
- **Recompute triggers**: when admin adds/edits a scheme (rebuild TF-IDF corpus + re-vectorize), or when farmer edits profile (recompute just that farmer's ranking — cheap, since vectorizer.transform() on one new doc is fast if you persist the fitted vectorizer).

---

## 8. Data model (core tables)

```
farmers(id, name, phone, state, district, village, category, gender, age,
        land_size, land_type, irrigation_source, crop, season,
        electricity_units, lpg_status, house_type, vehicle_owned,
        bank_account, kcc_status, evi_score, evi_bucket, created_at)

schemes(id, name, level[centre/state], state(nullable), department,
        category[loan/msp/subsidy/insurance/equipment/...], description_text,
        application_link, deadline, status, created_by_admin_id, created_at)

applications(id, farmer_id, scheme_id, match_score, matched_keywords[],
             missing_keywords[], status[submitted/under_review/approved/rejected],
             admin_remarks, applied_at, updated_at)

bookmarks(id, farmer_id, scheme_id, created_at)

admins(id, name, email, password_hash, role[super_admin/district_admin],
       district(nullable), state(nullable))

audit_log(id, actor_id, actor_type, action, entity, entity_id, timestamp)
```

---

## 9. Synthesizing your own dataset (since you must build this yourself)

You need two datasets: **(a) scheme corpus** and **(b) farmer profiles**.

**(a) Scheme corpus — semi-real, don't fabricate from scratch:**
1. Manually collect ~40–60 real scheme descriptions from public sources: PM-KISAN, PMFBY (crop insurance), Kisan Credit Card, PM-KUSUM (solar), Soil Health Card, e-NAM, PMFME, Rashtriya Krishi Vikas Yojana, PM Formalisation of Micro Food Processing Enterprises, plus 2–3 state schemes (e.g., Bihar's specific agri schemes, given your location, or pick 2–3 states for variety) — copy the *plain-language eligibility summary* (paraphrase in your own words for the admin's "pasted text," don't scrape verbatim off government sites to avoid any duplication concerns).
2. This gives you a legitimate, defensible dataset instead of a fake one — a judge asking "is this real data?" gets a confident "yes, drawn from official scheme guidelines."

**(b) Farmer profiles — fully synthetic, and that's fine + expected:**
- Write a small Python generator (`Faker` + custom distributions) that samples: state (weighted toward 5–6 states), land size (log-normal, skewed toward <2 ha to reflect India's actual small/marginal-farmer majority), category (roughly matching Census proportions), crop by state/season logic, and correlated economic-proxy fields (e.g., smaller land → lower probability of tractor/KCC, to keep the EVI meaningful rather than random noise).
- Generate 500–2,000 synthetic farmer profiles for your demo + reports. Document your generation assumptions in a README — that transparency is itself good practice and something judges reward.

---

## 10. Recommended tech stack

| Layer | Choice | Why |
|---|---|---|
| Frontend | **React + Tailwind CSS** (Vite) | Fast to build, matches your Farmer Dashboard/Admin Portal split with shared component library |
| Backend API | **FastAPI (Python)** | Same language as your ML/matching code — no context switch, auto-generated OpenAPI docs are great for a demo |
| Matching engine | **scikit-learn (TfidfVectorizer, cosine_similarity)** + custom regex rule layer | Matches your PS exactly, no GPU/embedding infra needed |
| Database | **PostgreSQL** | Relational integrity for farmers/schemes/applications; use JSONB columns for matched/missing keyword arrays |
| Auth | **JWT + role-based access control**, FastAPI's `OAuth2PasswordBearer` or a lightweight library like `fastapi-users` | Real authorization requirement in your spec |
| Caching (optional) | Redis for cached TF-IDF matrix / vectorizer pickle | Speeds up repeated matching without recompute |
| Dataset generation | **Faker (Python)**, pandas | For synthetic farmer profiles |
| Reports/dashboard viz | **Recharts / Chart.js** (frontend) or a small **Streamlit** admin-analytics add-on if you want to move fast | Choropleth map: `react-simple-maps` or Plotly for state/district shading |
| Deployment (hackathon) | Frontend on Vercel/Netlify, backend on Render/Railway, Postgres via Supabase/Neon | Free tiers, fast setup, good for live demo links |
| Notifications (mock) | Twilio sandbox or just a simulated in-app notification feed | Don't over-invest here for a hackathon |

---

## 11. Build roadmap (phase it — don't try to build all of §4 at once)

**Phase 1 — Core PS (must-have, get this rock solid first):**
1. Farmer profile form + DB
2. Scheme admin CRUD (paste text)
3. TF-IDF + cosine matching engine, ranked results
4. Matched/missing keyword highlighting
5. Bookmark button

**Phase 2 — Your extensions (differentiators):**
6. EVI economic scoring + proxy inputs
7. State/centre filter + scheme-type intent filter
8. Admin application inbox + status workflow
9. Auth/RBAC (farmer/admin/super-admin)

**Phase 3 — Reports & polish (judge-facing wow factor):**
10. District/state "Prominent Schemes" public page
11. Admin + public report dashboards (charts)
12. Synthetic dataset generation + seed script
13. Multilingual toggle, document checklist, or map viz — pick 1–2, don't do all

**Phase 4 — If time remains:**
14. Chatbot Q&A over scheme text
15. PWA/offline caching
16. Voice input

---

## 12. Things to say explicitly in your pitch (framing that will score well)

- **Problem → why myScheme doesn't fully solve it** for the agri vertical specifically.
- **EVI is a decision-support heuristic, not a legal determination** — shows maturity/responsibility.
- **Dataset provenance**: real scheme text (paraphrased from official guidelines) + documented synthetic farmer generation — shows rigor, not hand-waving.
- **No embeddings/LLM dependency for core matching** — keeps it fast, explainable, auditable (important for government-facing tools where opaque black-box scoring is a real trust problem) — this is actually a *feature*, not a limitation, and worth stating that way.
- **Public reporting layer** as a policy-transparency contribution, not just a farmer tool.

---

