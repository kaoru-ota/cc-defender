# Call Center Defender

**Call Center Defender** is a browser-based simulation game where the player manages a call center's 9-hour shift through staffing decisions alone. Instead of reacting reflexively to queue length or SL (Service Level), you practice *reading the forecast and moving staff proactively*.

## 🎬 Demo
PC
<p align="center">
  <img width="972" height="614" alt="CCD1" src="https://github.com/user-attachments/assets/f7aee21c-e5d4-4da2-b947-6ea58e0063f9" />
</p>
Mobile
<p align="center">
<img width="369" height="544" alt="CCD2" src="https://github.com/user-attachments/assets/03a0fb3b-21e7-4cef-bb53-df47d06253ab" />
</p>

## 🎯 What this tool is for

- A simulator for developing WFM (Workforce Management) intuition.
- Serves as a decision-making sandbox: when and how much to add or remove agents over the course of the day (OPENING → MORNING → LUNCH → AFTERNOON → EVENING → CLOSING).
- Gives you a feel for how hard it is to simultaneously satisfy two competing KPIs: **SL (Service Level)** and **Occupancy**.
- Designed to penalize both overstaffing (Occupancy < 70%) and understaffing (Occupancy > 90%), honing your instinct for finding the sweet spot.
- Also useful as a teaching tool for illustrating the gap between the **predicted values** from classical queueing models (Erlang C / Erlang X+) and the **actual measured values**.

## 👥 Who it's for

- Call center SVs (supervisors) / WFM staff who want to practice decision-making
- Trainers putting together onboarding material for new contact center agents
- Students / engineers interested in Erlang models and occupancy / utilization modeling
- Anyone who enjoys data-driven simulation games

## 📋 How the game works (read before you play)

### Winning conditions

- Run the full 9-hour shift and simultaneously satisfy **both of the following** to win:
    1. **Service Level of 80/20** — 80% of calls answered within 20 seconds (SL ≥ 80%)
    2. **Occupancy within 70%–90%**

### What the player can and cannot do

- The player can only **increase / decrease headcount (ADD / REDUCE)**.
- AHT (Average Handle Time), break and lunch scheduling, and the call volume itself — are all fixed and cannot be adjusted.
- Hiring is **not immediate**; there is a **10-minute time-to-seat delay** (simulating recruitment and onboarding).
- Headcount adjustments have a **15-minute cooldown** (= next adjustment available 15 min later); rapid-fire clicks won't work.

### Gotchas

- During **OPENING (first 30 min)**, call volume is ramping up, so Occupancy looks low. Don't panic-reduce — you'll be short later.
- During **LUNCH**, agents take their 1-hour lunch in staggered groups of 6. Adding staff during this window tends to leave you overstaffed in the second half.
- During **MORNING / EVENING**, groups of 6 agents rotate through 15-minute mini-breaks.
- **While Occupancy is out of range, an AHT penalty kicks in** (×1.10 below the lower bound, ×1.30 above the upper bound). Understaffing snowballs.
- **Even if SL dips temporarily, if Predict SL (landing projection) stays at 80%, "wait and see" is often the right call.** Reflexive ADDs cost you by dragging Occupancy down.

### System requirements

- Runs on any modern browser (Chrome / Safari / Firefox / Edge).
- Self-contained in a single HTML file. No server or external libraries required.
- Responsive: designed for desktop (recommended: 960px+ width), tablet, and smartphone (portrait and landscape).

### 💻 Desktop vs 📱 Mobile

- **Desktop / mobile landscape (recommended)**: **All controls** — SPEED slider, MODEL switch, and PAUSE button — stay visible at the bottom of the screen. Strongly recommended for training sessions, where you want to read the numbers carefully before deciding.
- **Mobile portrait**: To maximize vertical space, SPEED / MODEL / PAUSE are hidden and **only CHANGE / APPLY** remain accessible. Rotate your device to landscape if you want to change speed or switch models.
- **Minimum recommended viewport width: 960px.** Below this, Forecast chart tick labels and the KPI panel may shrink and become hard to read.
- **Keep browser zoom at 100%.** Extreme zoom levels can break the layout.
- On mobile, an incoming call or app switch may suspend the browser tab and cause the simulation to fall out of sync. For long sessions, **hit PAUSE explicitly** before stepping away.

## 🎮 Basic controls

- Use the **CHANGE** ＋ / － buttons to choose how many agents to add or remove, then **APPLY** to commit.
- Use the **SPEED** slider to adjust simulation speed (1×–1000×).
- **PAUSE** pauses — useful when you want a moment to step back and assess the situation.
- Use **MODEL** to switch the prediction model (Erlang C / Erlang X+).

## 💡 Tips to get better

- Watch the **Forecast chart**'s teal line (Forecast) — always anticipate how call volume will change in the next phase. When the green Actual line persistently sits below Forecast, in-SL throughput is running behind plan.
- The **System Comment** panel's HINT recommends an action based on the current situation. When in doubt, start by reading this.
- If **Predict SL** starts dipping below target, watch the cooldown and ADD early. Reacting after actual SL drops is usually too late.
- Be careful not to overstaff at the **MORNING → LUNCH** transition — you tend to end up overstaffed in the second half of LUNCH.

## 📊 How to read the Forecast chart (CALL VOLUME FORECAST)

The Forecast chart at the top of the screen overlays two lines on the same **calls/hr** Y-axis scale. It lets you compare Forecast (plan) and Actual (measured) side-by-side.

### What the two lines mean

- **Forecast (teal / #6dbfc2)**: Planned call volume per phase, drawn as a **piecewise line**. It stays flat within each phase and only slopes during the OPENING / CLOSING ramps. The vertical steps at phase boundaries are intentional — they reflect the granularity at which the plan is actually specified ("this phase is staffed for this call rate").
- **Actual (green / #00ff41)**: Measured throughput of **calls answered within SL**, smoothed by a **15-minute rolling window** and converted to a per-hour rate (calls/hr). Same scale as Forecast, so gaps between plan and reality are visually obvious.

### What to read from the chart

- **Actual below Forecast**: In-SL throughput is not keeping up with the plan. You may be understaffed, or the Occupancy-overload AHT penalty (×1.30) is kicking in. Consider ADD early.
- **Actual above Forecast**: In-SL throughput is running ahead of plan — you have headroom. Depending on the next phase's call rate, you can consider REDUCE.
- **Beware the lag**: Actual is a 15-minute rolling average, so it responds slowly right after a phase transition or a spike. Treat it as a **medium-term trend indicator**; for real-time decisions, combine it with the KPI panel (SL / Predict SL / Occupancy) below the chart.

## 📐 About the Erlang models (supplementary)

A quick intro for newcomers to **Erlang C** and **Erlang X+**, the two models selectable via MODEL.

### What is "Erlang", exactly?

- A family of classical queueing-theory models invented by Danish mathematician **A. K. Erlang (1878–1929)** to design telephone switchboard trunk capacity.
- The unit **erlang (erl)** represents offered load, defined as **arrival rate × average service time**. Example: 60 calls/hour at 5 minutes each → 5 erl.

### The model family

- **Erlang B**: All trunks busy → call **blocked** (no queue). Used for trunk / circuit capacity design.
- **Erlang C**: All callers queue and wait indefinitely. **No abandonment.** The foundational call-center staffing model, published by Erlang himself in 1917.
- **Erlang A (industry name: Erlang X)**: Erlang C **with abandonment**. Customers abandon the queue after their patience runs out. The "A" stands for Abandonment. Major WFM vendors (NICE, Verint, Genesys, etc.) often call it "Erlang X".
- **Erlang X+ (this game's name)**: An extension of Erlang A that adds the following 6 real-world effects:
    1. **Redial**: 20% of abandoned callers retry
    2. **Finite queue**: Queue caps out at 500 calls and blocks further arrivals
    3. **Occupancy-based AHT penalty**: AHT inflates to ×1.10 / ×1.30 when Occupancy is outside 70–90%
    4. **Shrinkage-adjusted seat count**: Accounts for seats temporarily vacated by lunch and mini-breaks
    5. **Overflow AHT inflation**: Agents beyond the initial seat count are assumed slower than tenured agents, so their AHT is ×1.5
    6. **10-min lead-time projection**: Combine current actuals with the steady-state SL of the remaining time to estimate where this will land if we hold current staffing

### When each model applies in-game

- **Light load (low Occupancy / high SL)** → Erlang C matches reality. Abandonment and redial are near zero, so the simpler formula suffices.
- **Overloaded (high Occupancy / low SL)** → Erlang X+ matches reality. Erlang C ignores abandonment, so it severely underestimates SL in overload (ρ ≥ 1).
- Rule of thumb: once **utilization ρ = A / s** exceeds 0.85, X+ gives a more realistic forecast.

### Key formulas (for those who want the math)

- **Erlang C**: $C(s, A) = \frac{A^s/s! \cdot s/(s-A)}{\sum_{k=0}^{s-1} A^k/k! + A^s/s! \cdot s/(s-A)}$, $SL(T) = 1 - C \cdot e^{-(s-A)T/\text{AHT}}$
- **Erlang X+**: Solve for the effective arrival rate $\lambda_{eff}$ by fixed-point iteration $\lambda_{eff} = \lambda_0 + r \cdot P_{abandon} \cdot \lambda_{eff}$, derive $P_{block}, P_{delay}$ from steady state, and compute SL on a served-rate basis.

### Built-in self-check

- Running `window.__verifyErlang()` in the browser console executes 20+ regression tests (textbook values, monotonicity, boundary conditions, numerical stability under overload). Use it as a safety net when modifying the prediction code.

## ⚠️ Disclaimer

- This tool is a simulation built for learning, practice, and entertainment. It does not represent numbers you would see in a real call-center operation.
- Parameters (AHT, patience time, redial rate, etc.) use generic values and do not reflect the actual performance of any specific industry or company.

## 📄 License

Released under the **MIT License**. See [`LICENSE`](./LICENSE) for details.

## 👤 Author

**Kaoru Ota**
