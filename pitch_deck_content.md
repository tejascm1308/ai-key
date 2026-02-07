# REBASE AI KEYBOARD - Pitch Deck Content

---

## SLIDE 1: INTRO

### Visual

- **Logo:** REBASE (stylized with keyboard icon)
- **Tagline:** "Your AI That Types With You"

### One-Liner

**"The only AI keyboard that saves you 30+ minutes daily, works everywhere, and keeps your data 100% private on your device."**

---

## SLIDE 2: THE PROBLEM

### Headline

**Knowledge Workers Lose 2+ Hours Daily to Repetitive Typing**

### Key Pain Points

**💼 The Daily Struggle:**

- 150+ emails, 50+ messages, 3+ documents per day
- Context switching across 8-12 apps
- Repetitive phrasing, formatting, tone adjustments
- **Lost Time: 30-60 minutes/day = 125-250 hours/year**

**❌ Current Solutions Fall Short:**

- **Grammarly:** Web-only, no app awareness
- **GitHub Copilot:** Code-only, no email/docs
- **ChatGPT:** Requires copy-paste, context switching
- **Privacy Concerns:** All send data to cloud

### Market Size

**500M+ laptop users typing >1 hour/day**

- 100M knowledge workers
- 50M developers
- 30M students

---

## SLIDE 3: THE SOLUTION

### Headline

**REBASE: Your Intelligent AI Keyboard Everywhere**

### Core Value Proposition

**An AI keyboard that understands context, learns your style, and suggests completions in ANY app—Gmail, Slack, VS Code, Word, WhatsApp—100% offline.**

### Key Features

**🎯 Universal Integration**

- Works in every app (email, code, docs, chat)
- No context switching required

**🧠 Context-Aware Intelligence**

- Detects app type, mood, formality level
- Learns your patterns over time (LTM)
- 70%+ acceptance rate

**⚡ Lightning Fast**

- 50-150ms response time (3x faster than cloud)
- Mood-aware timing (never interrupts)

**🔒 Privacy Guaranteed**

- 100% on-device processing (RunAnywhere SDK)
- Zero cloud transmission (architectural impossibility)
- HIPAA compliant, works offline

**📊 Quantified Impact**

- Saves 30-60 minutes/day
- Tracked via acceptance rate × typing speed

---

## SLIDE 4: SOLUTION ARCHITECTURE

### Headline

**Complete On-Device Data Flow**

### Architectural Guarantee

**"NO DATA LEAVES THE DEVICE"**

### Architecture Diagram

_(Use the attached image format - complete on-device data flow)_

**Components (Top to Bottom):**

1. **MOBILE APP (iOS/Android)**
   - Host Environment

2. **CUSTOM KEYBOARD EXTENSION**
   - UI | Voice Button | Settings

3. **SHORT-TERM MEMORY (STM)**
   - Current Session Context | Deleted on Close

4. **CONTEXT DETECTION ENGINE**
   - <50ms | App ID, Field Type, Mood

5. **SMART CACHE (3-Tier, 40-50% Hit)**
   - Tier 1: Exact Match
   - Tier 2: Pattern Cache
   - Tier 3: Phrases

6. **RUNANYWHERE SDK - LLM ROUTER**
   - **Qwen 2.5** (Simple, 85%)
   - **Phi-4 Mini** (Complex, 10%)
   - **Moonshine** (Voice)

7. **LONG-TERM MEMORY (LTM)**
   - Encrypted Local Storage | Patterns Only (No PII)

### Privacy Lock (Side Panel)

**PRIVACY LOCKED**

- Zero cloud transmission
- No network permissions
- HIPAA COMPLIANT

---

## SLIDE 5: SECRET SAUCE

### Headline

**4 Competitive Moats That Can't Be Copied**

### 1. Privacy Moat

**Architectural Guarantee, Not Policy**

- 100% local execution via RunAnywhere SDK
- No network permissions = impossible to leak data
- Competitors rely on cloud (OpenAI, Anthropic)

### 2. Technology Moat

**90%+ Gross Margins**

- Zero GPU costs (runs on user's device)
- Competitors pay $0.10-0.50 per user for cloud LLMs
- Impossible to match price without local execution

### 3. Data Moat

**Personalization Gets Better Over Time**

- LTM learns your unique patterns for 6+ months
- Competitors can't replicate your learned data
- Switching cost increases exponentially

### 4. Speed Moat

**3x Faster Than Cloud**

- On-device inference: 50-150ms
- Cloud LLMs: 200-500ms
- Users can feel the difference

---

## SLIDE 6: BUSINESS MODEL

### Headline

**Freemium SaaS: Free Forever → Pro Unlimited**

### Pricing Tiers

| Tier      | Price                | Features                                                                               | Target                        |
| --------- | -------------------- | -------------------------------------------------------------------------------------- | ----------------------------- |
| **Free**  | $0                   | 2 hours/day AI time<br>All apps supported<br>Mood detection<br>Basic learning          | 90% of users<br>(acquisition) |
| **Pro**   | $8/month<br>$80/year | 24/7 unlimited<br>Voice input<br>Advanced AI (Phi-4)<br>Cross-device sync<br>Analytics | 5-15% conversion<br>(revenue) |
| **Teams** | $12/user/mo          | All Pro +<br>Team analytics<br>Admin dashboard<br>Custom training                      | Enterprises<br>(future)       |

### Unit Economics

```
CAC:              $3 (70% organic, 30% paid)
LTV:              $180 (18-month retention)
COGS:             ~$0 (local execution)
Gross Margin:     92%
LTV:CAC:          60:1 ✅
Payback:          <1 month ✅
```

### Why This Works

- **Zero COGS:** No GPU costs (RunAnywhere SDK)
- **Infinite Scale:** 1M users = same cost as 1K users
- **High Margin:** 92% vs SaaS avg 70-80%

---

## SLIDE 7: MARKET & GO-TO-MARKET

### Total Addressable Market (TAM)

**Global Market:**

- **500M+** laptop/desktop users typing >1 hour/day
- **2B+** mobile keyboard users (future)

**Serviceable Markets:**

- **SAM:** 150M (knowledge workers + developers)
- **SOM:** 1M users Year 1 (0.2% penetration)

### Customer Segments

| Segment           | Size | Pain Point        | Willingness to Pay |
| ----------------- | ---- | ----------------- | ------------------ |
| Knowledge Workers | 100M | Email/doc fatigue | $8-10/mo           |
| Developers        | 50M  | Code everywhere   | $8-10/mo           |
| Students          | 30M  | Writing speed     | $5-8/mo            |
| Creators          | 20M  | Content volume    | $8-10/mo           |

### Go-To-Market Strategy

**Phase 1: Product-Led Growth (Months 1-3)**

- Chrome Web Store launch (500-1K installs/week)
- Product Hunt featured launch
- Viral loops: Time-saved badges, referrals
- Expected: 10K users, 200 Pro ($1.6K MRR)

**Phase 2: Paid Acquisition (Months 4-12)**

- Google/Facebook ads (CAC <$5)
- Influencer partnerships (Ali Abdaal, Fireship)
- Content marketing (TikTok, YouTube Shorts)
- Expected: 100K users, 8K Pro ($64K MRR)

**Viral Coefficient:** k=0.3 (organic compounding)

---

## SLIDE 8: COMPETITIVE LANDSCAPE

### Headline

**We Win on Privacy, Price, and Universal Integration**

### Competitive Matrix

|                       | REBASE     | Grammarly   | Copilot      | ChatGPT       |
| --------------------- | ---------- | ----------- | ------------ | ------------- |
| **All Apps**          | ✅         | ❌ Web only | ❌ IDE only  | ❌ Copy-paste |
| **Context-Aware**     | ✅         | ❌          | ⚠️ Code only | ❌            |
| **Learns Your Style** | ✅         | ❌          | ⚠️           | ❌            |
| **100% Private**      | ✅ Local   | ❌ Cloud    | ⚠️ Hybrid    | ❌ Cloud      |
| **Works Offline**     | ✅         | ❌          | ❌           | ❌            |
| **Free Tier**         | ✅ 2hr/day | ✅ Limited  | ❌           | ✅ Limited    |
| **Price**             | **$8/mo**  | $12-15/mo   | $10/mo       | $20/mo        |

### Our Positioning

**"The only AI keyboard that actually understands you, works everywhere, and never sends data to the cloud."**

### Market Opportunity

- Grammarly: 30M users, $400M ARR (web-only)
- GitHub Copilot: 10M users, $1B+ ARR (code-only)
- **REBASE TAM:** 5x larger (universal)

---

## SLIDE 9: 3-YEAR PROJECTIONS

### Financial Projections

| Metric              | Year 1 | Year 2 | Year 3 |
| ------------------- | ------ | ------ | ------ |
| **Free Users**      | 100K   | 500K   | 2M     |
| **Pro Users**       | 8K     | 50K    | 200K   |
| **Conversion Rate** | 8%     | 10%    | 10%    |
| **MRR**             | $64K   | $400K  | $1.6M  |
| **ARR**             | $768K  | $4.8M  | $19.2M |
| **Gross Margin**    | 92%    | 92%    | 90%    |
| **CAC**             | $3     | $4     | $5     |
| **LTV:CAC**         | 60:1   | 45:1   | 36:1   |

### Key Metrics Projections

| Metric                  | Year 1 | Year 2 | Year 3 |
| ----------------------- | ------ | ------ | ------ |
| **Time Saved/User/Day** | 35 min | 42 min | 50 min |
| **Acceptance Rate**     | 72%    | 78%    | 82%    |
| **Monthly Churn**       | 6%     | 5%     | 4%     |
| **NPS Score**           | 60     | 68     | 72     |
| **Desktop Users**       | 70K    | 300K   | 1M     |
| **Mobile Users**        | 30K    | 200K   | 1M     |

### Revenue Breakdown (Year 3)

- Pro Subscriptions: $19.2M (96%)
- Teams/Enterprise: $800K (4%)
- **Total ARR: $20M**

### Path to Profitability

- **Month 3:** Break-even (66 Pro users)
- **Year 1:** $520K profit (68% margin)
- **Year 2:** $3.4M profit (71% margin)
- **Year 3:** $14M profit (73% margin)

---

## SLIDE 10: CURRENT STATUS & THE ASK

### Where We Are Today

**✅ Accomplishments to Date:**

- MVP built with RunAnywhere SDK integration
- 100% on-device AI working (Qwen2.5 + Phi-4 + Moonshine)
- Context detection engine validated (95%+ accuracy)
- Windows desktop app functional
- iOS keyboard extension in beta
- STM + LTM architecture implemented
- Security audit passed (HIPAA compliant)

**📊 Early Traction:**

- 50 beta users (internal testing)
- 78% acceptance rate (target: 70%+)
- 42 min/day average time saved
- 4.8/5 beta satisfaction score

### What We're Building Next

**Q1 2026 (Next 3 Months):**

- [ ] Chrome Web Store launch
- [ ] Product Hunt launch
- [ ] iOS App Store release
- [ ] 10K free users
- [ ] 200 Pro subscribers
- [ ] $1.6K MRR

### The Ask: $500K Seed Round

**Use of Funds:**

| Category                | Amount      | Purpose                                        |
| ----------------------- | ----------- | ---------------------------------------------- |
| **Product Development** | $150K (30%) | Android keyboard, voice features, advanced LTM |
| **Marketing & Growth**  | $200K (40%) | Paid ads, influencers, content marketing       |
| **Team Expansion**      | $100K (20%) | 2 engineers, 1 product marketer                |
| **Operations**          | $50K (10%)  | Legal, infrastructure, runway                  |

**Timeline:** 12-month runway to $50K MRR (profitability)

### Milestones with This Capital

**Month 6:**

- 50K users, 2.5K Pro, $20K MRR

**Month 12:**

- 100K users, 8K Pro, $64K MRR
- Break-even profitable
- Series A ready ($5M at $25M valuation)

**Month 18:**

- 250K users, 25K Pro, $200K MRR
- Platform for strategic acquisition talks

### Exit Potential

**Strategic Buyers:**

- Microsoft (Office + Windows integration)
- Google (Workspace + Android)
- Apple (iOS + macOS)

**Comparable Exits:**

- Grammarly valuation: $13B (2021)
- GitHub Copilot: Part of $7.5B GitHub acquisition
- **REBASE potential:** $50-100M at 10M users

---

## APPENDIX: WHY NOW?

**3 Tailwinds Making This Possible:**

1. **RunAnywhere SDK Breakthrough (2025)**
   - 88% model compression (41GB → 4.3GB)
   - Cross-platform deployment (iOS/Android/Windows)
   - Made on-device AI keyboards feasible

2. **Privacy Regulations Tightening**
   - GDPR, CCPA, HIPAA enforcement
   - Users demand local-first solutions
   - Cloud AI = liability

3. **On-Device AI Mainstream**
   - Apple Intelligence, Google Pixel AI
   - Users now expect fast, private AI
   - Market educated, ready to pay

**This window won't last—first mover wins the category.**

---

## CLOSING SLIDE

### Vision

**"A world where everyone types faster, thinks clearer, and never sacrifices privacy."**

### Contact

- **Website:** rebase.ai
- **Email:** founders@rebase.ai
- **Demo:** [QR code to live demo]

**Let's build the keyboard everyone deserves. 🚀**
