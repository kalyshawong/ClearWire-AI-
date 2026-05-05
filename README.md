# ClearWireAI

> **An AI financial copilot for international students.**
> By 2030, moving money is solved. The agent layer is what's open.

🔗 **[Live Demo](https://clearwire.vercel.app)** &nbsp;·&nbsp; 📄 [Pitch Deck](./ClearWireAI-Pitch-2030.pdf)

---

## The problem

International students wait an average of 30 days a year for money to arrive and lose ~$2,000 a year to wire fees and FX spread.

The wire is 1970s infrastructure. Dad sends Monday, the money lands Sunday, rent was due Friday. HSBC takes $230 in fees and a 2–3% hidden FX spread on every transfer. While the family waits, scammers target the gap with AI-cloned parent voices and fake tuition invoices.

The wire is the problem — not the people.

## The thesis

By 2030, the underlying infrastructure for cross-border money is built. mBridge moves yuan across the China–HK border. Project Nexus connects India and Singapore. AliCNH, USDC, and other regulated stablecoins are interchangeable settlement formats. FedNow gives the U.S. 24/7 real-time settlement.

**The rails are commodity.**

What's open is the **agent layer** — the intelligence that decides *when* to convert, *how much*, on *what rate*, and *which combination of rails* to use for each family. ClearWireAI fills it.

## Five agents, one Vault

| Agent | Role |
|---|---|
| **FX Agent** | The orchestrator. Routes Dad's transfer through optimal rail composition + dynamically times the FX swap. *(Deep dive below.)* |
| **Bills Agent** | Auto-pays rent, tuition, utilities, subscriptions on schedule from the Vault. Never late because it never hits the last minute. |
| **Budget Agent** | Tracks variable spending — groceries, ride-share, dining. Quiet category nudges when pacing over. |
| **Scam Agent** | Intercepts pig-butchering scams, voice-cloned parent calls, and fake tuition invoices *before* money moves. Trained on patterns specific to international students. |
| **Family Bridge** | Mom funds an Essentials Bucket (4.1% APY) that Bills Agent draws from when the Vault runs low. Mom gets a Sunday summary instead of a phone call every time. |

The Vault is a single USD-denominated account earning 4.1% APY, with a debit card linked. Same architecture as Wealthfront Cash.

---

## The FX Agent

The centerpiece. The agent that earns its keep.

### Interaction model

1. **Dad initiates a transfer** in his usual app (WeChat Pay, ICBC Mobile, HSBC, etc.).
2. **ClearWireAI catches the inbound wire** before it lands — typically 4 hours before settlement, while it's moving through mBridge.
3. **The FX Agent forecasts the corridor** — reads CNH/USD rate history, current spread vs. mid, the family's bill calendar, and produces a **proposed conversion schedule.**
4. **Kalysha sees the proposal** with reasoning — *"3-way split over 5 days. 30% today, 50% Wednesday at the predicted weekly low, 20% Friday. Saves ~$18 vs. converting all at once."*
5. **One-tap approve.** The agent executes the schedule autonomously over the next several days. No further input needed.

### Dynamic-split logic

The FX Agent does not run a fixed rule like "always convert 30% today, 50% tomorrow." That's dollar-cost averaging — dumb, predictable, and easy to beat.

Instead, the agent **predicts the rate cycle** and weights conversions by **confidence in each predicted low.**

```
Volatile corridor week  →  8 windows split across 5 days
Typical China week      →  3 windows, 30/50/20 weighted by confidence
Stable HK corridor      →  2 windows, 60/40
Today is the weekly low →  Convert all of it now
```

**The split is the outcome of the prediction, not a fixed rule.** The shape itself is the agent's bet.

### Three corridors, three rail compositions

The FX Agent auto-routes per corridor. Each corridor uses a different combination of sovereign and stablecoin infrastructure:

#### 🇨🇳 Mainland China — 4 rails, ~25 seconds

```
CNY  →  e-CNY  ─────────  AliCNH  →  USDC  →  USD
        (mainland bank)   (mBridge,    (HK VASP    (FedNow)
                          cross-border) swap desk)
```

| Step | Time | Cost | What happens |
|---|---|---|---|
| Mainland bank on-ramp | ~3 sec | 0.01% | ICBC / Bank of China tokenizes CNY into e-CNY for cross-border use |
| **mBridge** | ~7 sec | 0.02% | BIS-led sovereign cross-border ledger (PBOC ↔ HKMA). The only legal way to move yuan value out of mainland China at scale. |
| **VASP** | ~7 sec | 0.01% | HK-licensed swap desk converts AliCNH → USDC at the offshore yuan rate. **This is where the FX Agent's timing earns its keep.** |
| **FedNow** | ~8 sec | Free | USDC → USD settlement to the Vault |

USDC isn't legal in mainland China, so mBridge isn't optional — it's the regulatory plumbing. The actual FX swap happens at the HK leg, where AliCNH (the regulated offshore yuan stablecoin issued by Ant Group) gets converted to USDC at the moment the agent predicts is optimal.

#### 🇭🇰 Hong Kong — 3 rails, ~15 seconds

```
HKD  →  digital HKD  →  USDC  →  USD
        (HK bank        (VASP swap)  (FedNow)
        on-ramp)
```

HK is the bridge city itself — no cross-border ledger needed. HKD is pegged to USD at ~7.80 (HKMA band: 7.75–7.85), so there's barely any rate movement to time. The FX Agent's job here is mostly about speed and low fees, not FX timing.

#### 🇮🇳 India — 3 rails, ~20 seconds

```
INR  →  Singapore via Nexus  →  USDC  →  USD
        (cross-border + swap          (FedNow)
        combined)
```

Project Nexus combines the cross-border step and the swap step into one operation. INR is more volatile than HKD, so the FX timing actually pays off — and the corridor's volatility means the agent typically splits across 6–8 small windows to average down.

### Five optimization layers

| # | Layer | Mechanism | Avg savings/mo |
|---|---|---|---|
| 01 | **Rail-agnostic routing** | Auto-picks cheapest rail composition per corridor | ~$5 |
| 02 | **Predictive lows** | Forecasts rate cycle, converts on confidence-weighted optima | ~$15 |
| 03 | **Pre-staging** | When parents fund ahead of a known bill, agent spreads conversions across more days for better timing | ~$10 |
| 04 | **Family pooling** | 10K+ families pooled to access wholesale FX desks (~0.05% spread vs ~1.5% retail bank rate) | ~$12 |
| 05 | **Forward hedging** | Locks next semester's tuition rate today, protecting against currency weakness | ~$8 |
| | **Total** | | **~$50/mo** |

### What it saves a family

For an average mainland Chinese family sending ~$60K/year to a U.S. graduate student:

| Source | Savings |
|---|---|
| Wire fee savings vs. HSBC ($231 → $1.20 × 8 transfers/yr) | **~$1,847 / year** |
| FX optimization layers stacked | **~$50 / month** = $600 / year |
| **Total** | **~$2,447 / year** |

This is the difference between a family stretching and a family covering rent on time.

---

## Other agents (brief)

- **Bills Agent** — schedules rent, tuition, utilities, subscriptions for the next 30 days from the Vault. Renders the upcoming bill calendar with rolling balance ("after all payments: $177 remaining; Dad's transfer restocks Vault to $6,377").
- **Budget Agent** — categorizes variable spending and surfaces nudges. *"You've used 80% of your dining-out budget by the 18th — want me to suggest meal-prep options?"*
- **Scam Agent** — caught a $4,200 pig-butchering attempt on a recent demo run. Three signals flagged it: known scam wallet, six-week grooming timeline on Telegram, and 23 similar attempts on Asian international students that month. Model trained on this exact pattern.
- **Family Bridge** — Mom contributes $800 to Kalysha's Essentials Bucket at 4.1% APY. If the Vault runs low, Bills Agent quietly pulls from Mom's bucket. Mom gets a Sunday summary, not a phone call every time. *Contribution, not loan.*

## Live demo

The interactive prototype walks through:

- Home page with Vault balance, agents at-a-glance, FX Agent proactive notification
- FX Agent reasoning chat — corridor analysis, predicted lows, proposed schedule
- Proposal card with one-tap approval
- Three corridor toggles (China / HK / India) — each shows its own dynamic-split shape
- Bills Agent calendar with rolling Vault balance
- Family Bridge with Mom's Essentials Bucket
- Scam Agent intercept

🔗 **[clearwire.vercel.app](https://clearwire.vercel.app)**

## Screenshots

> Add 4–6 screenshots here.

| Home | FX Agent reasoning | Proposal card | Family Bridge |
|---|---|---|---|
| ![Home](./screenshots/home.png) | ![FX](./screenshots/fx-agent.png) | ![Proposal](./screenshots/proposal.png) | ![Family](./screenshots/family.png) |

## Tech stack

- **Prototype:** HTML, CSS, JavaScript (single standalone file, no build step)
- **Styling:** Tailwind CSS
- **Hosting:** Vercel
- **Deck:** PowerPoint, generated programmatically via python-pptx
- **Dynamic dates:** All UI dates auto-render from `new Date()` so the demo always feels current

---

> **By 2030, moving money is solved.**
> **The agent layer is what's open.**
>
> *Call home about your week. Not for money. Let us handle it.*

---

**Built by Kalysha Wong** &nbsp;·&nbsp; [LinkedIn](https://linkedin.com/in/kalyshawong) &nbsp;·&nbsp; April 2026

## License

MIT
