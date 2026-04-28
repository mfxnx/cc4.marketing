# FXNX — Task & Priority Backlog

> Running list of identified tasks, ideas, and strategic priorities.
> Add, reprioritize, or check off items as you go.

---

## 🔴 High Priority

- [ ] Complete `product-marketing-context.md` (in progress)
- [ ] Define primary target markets based on CRM data (Iran, Iraq, Brazil, Romania as starting point)
- [ ] Identify why high-traffic markets (Iran, Pakistan, Nigeria) have <5% conversion rate — likely inflated by incentive-driven junk registrations (iPhone giveaway campaigns etc.), not real traders
- [ ] **Fix withdrawal latency** — #1 complaint about competitors AND appearing in fxnx own support tickets. Resolve before building messaging around "fast withdrawals". — likely inflated by incentive-driven junk registrations (iPhone giveaway campaigns etc.), not real traders
- [ ] Establish clean segmentation model:
  - **Persona layer** (depositors only, split into 3):
    - Activated traders: deposited + traded → build personas from this group
    - Funded inactive: deposited, never traded → onboarding/UX problem
    - High-value / repeat depositors → primary ICP
  - **Conversion layer** (separate, not mixed into personas):
    - High-intent non-depositors: visited deposit page / started KYC → friction problem to fix
    - Low-intent / incentive-driven signups → ignore strategically
  - From dev/MT5: pull activated traders by first deposit size, time-to-deposit, instrument traded, geography, retention

---

## 🟡 Medium Priority

- [ ] **Post-registration survey** — ask new signups: why did you choose fxnx? what broker did you use before? what do you trade most? (needed to fill gaps in trader profile & acquisition data)
- [ ] **Account managers & support interview** — ask team: top complaints about previous brokers, why clients chose fxnx, biggest fears when opening account, most common support issues after joining, **which brokers do clients mention most as their previous broker?**
- [ ] Pull GA4 age/demographics report to understand trader age range
- [ ] Pull MT4/MT5 back-office report: most traded instruments, avg lot size, trading frequency
- [ ] Build out personas for top 2-3 trader profiles
- [ ] Audit current acquisition channels — understand where registrations are coming from
- [ ] Develop messaging for high-conversion markets (Romania, Brazil, Iraq)
- [ ] Create competitor analysis for top 3 forex brokers competing in MENA/LatAm

---

## 🟢 Later / Backlog

- [ ] **Build forex research agent** — custom sub-agent in `.claude/agents/` pre-loaded with fxnx context for competitive intel, market research, regulatory updates, ad benchmarks. Build after `product-marketing-context.md` is complete.


- [ ] **Iran connectivity fix** — technical solution (mirror domain / proxy) to recover users lost to internet shutdown. Iran is #1 market by deposits ($175K), high ROI when prioritized.
- [ ] Set up `product-marketing-context.md` skills integration across all 40 skills
- [ ] Run `/seo-audit` skill on fxnx.com
- [ ] Run `/page-cro` skill on landing page and sign-up flow
- [ ] Run `/analytics-tracking` skill to audit conversion tracking

---

## 💡 Ideas (not yet prioritized)

- Morocco: 100% retention + $2,058 avg FTD with only 2 depositors — could be a high-value untapped market
- Vietnam: 98.1% retention — worth a dedicated retention/upsell campaign
- Sweden: 1 depositor, 121.7 avg lots/FTD — outlier worth investigating

---

- [ ] **Proof points from dev** — total trading volume (lots or USD), avg execution speed (ms), total trades executed, uptime % since launch
- [ ] **Total depositor count** — exact number across all 40 countries
- [ ] **Testimonials/reviews** — ask account managers for any positive client feedback, screenshots, messages
- [ ] **Confirm competitors** — validate opofinance, wingo markets, mondfx with account managers (which brokers do clients mention most?)
- [ ] **Brand voice definition** — formalize tone, style, personality for fxnx (currently undefined)
- [ ] **Customer language** — collect verbatim phrases traders use to describe their problems and fxnx (from support chats, reviews, social)

## ✅ Done

- Installed `coreyhaines31/marketingskills` (40 skills in `.claude/skills/`)
- Confirmed MISA regulation + restricted jurisdictions (USA, Japan, Canada, Australia, China, Myanmar, North Korea)

