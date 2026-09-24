# 1. My Story & STAR Bank

The single highest-return file here. Interviewers spend 60–70% of the time on *your* resume. Know it cold.

---

## A. The 90-second pitch ("Tell me about yourself")

Structure: **Where I'm from → What I do now → Proof → What I want next.**

> "I'm Rahul, an Engineering Physics graduate from IIT Delhi, class of 2019. For the last four and a half years I've been building the analytics function at Burger Singh, a QSR chain with 140+ outlets across India — I'm currently Assistant Manager in the analytics department.
>
> My work sits at the intersection of data engineering and business decision-making. I own our BigQuery data layer, and I led the migration of our entire reporting stack from Looker Studio to Power BI — which, alongside query optimisation, cut our BigQuery consumption by 35–40% and saved roughly ₹1.5–2 lakh a month.
>
> The part I enjoy most is analysis that changes operations. I built a stockout-tracking system from third-party aggregator logs that showed we were losing 10–12% of potential sales to items going out of stock, and that drove a recovery of 2–4% within a quarter. I also built our food-cost and margin model — recipe-level costing joined to product sales, commissions and discounts — which is what leadership uses to judge outlet profitability.
>
> I'm looking to move into a senior analyst or analytics manager role where I can work at larger data scale and own a broader business area, ideally still in consumer, retail or food-tech where my domain knowledge compounds."

**Tuning it**
- Product Analyst role → lead with the stockout analysis and customer-experience KPIs.
- Manager role → lead with team handling, the platform migration decision, the 15-parameter scorecard.
- Consulting → lead with structuring: 140 outlets, 15 parameters, thresholds, scoring.

---

## B. "Why are you looking to move?"

Never criticise Burger Singh. Use the growth framing.

> "I've had a great run here — I effectively built the analytics function from the reporting layer up and owned it end to end. What I'm missing now is scale and specialisation. We're a 140-outlet business, so my data volumes and problem types have plateaued. I want to work where analytics is a larger, more mature function, with an experimentation culture, a bigger data platform, and senior analysts I can learn from. That's the step I can't take internally."

**Follow-up you will get: "Why now, after 4.5 years?"**

> "I wanted to finish the two things I'd started — the Power BI migration and the margin model — and hand them over properly rather than leave them half-built. Both are stable and documented now."

---

## C. The six STAR stories

Fill every `[...]` with your real numbers before you interview. Vague answers here destroy credibility faster than a wrong SQL query.

### STAR 1 — Cost Optimisation / Power BI Migration  *(flagship technical story)*

- **Situation:** Reporting ran on Looker Studio over BigQuery. Queries were unoptimised and every dashboard refresh re-scanned full tables, so the BigQuery bill climbed month over month and dashboards were slow for regional managers.
- **Task:** Bring cost down without losing reporting coverage. I proposed going further — replatforming to Power BI.
- **Action:**
  - Audited BigQuery usage via `INFORMATION_SCHEMA.JOBS` to find the highest-cost queries and the dashboards behind them.
  - Rebuilt the data layer: partitioned tables by date, clustered on outlet/brand, and created pre-aggregated summary tables so dashboards read small tables instead of raw event data.
  - Replaced repeated raw-table scans with scheduled incremental materialised tables.
  - Designed the Power BI semantic model — star schema, one sales fact, conformed date/outlet/item dimensions — and rewrote metric definitions as DAX measures so every dashboard used one definition of "net sales".
  - Ran both stacks in parallel, reconciled outputs number by number, trained the ops team, then decommissioned Looker Studio.
- **Result:** 35–40% reduction in query consumption, ~₹1.5–2L/month saved. One source of truth for metrics, so the "your number doesn't match mine" arguments in review meetings stopped.
- **Learning:** Cost optimisation is really data modelling. The bill was a symptom of the model, not the tool.

> **They will ask: "Why Power BI over Looker Studio?"**
> Semantic model plus DAX (Looker Studio has no real measure layer), row-level security for franchise partners, better handling of large models via import mode and incremental refresh, and the org was already on Microsoft 365 so licensing and SSO were solved.

> **"How exactly did you cut 35–40%?"**
> Partitioning and clustering so queries scan a date slice instead of the full table; pre-aggregation so a dashboard reads thousands of rows instead of millions; killing duplicate queries that different dashboards ran for the same metric; and moving from live queries on every dashboard interaction to scheduled import refreshes.

### STAR 2 — Item Stockout Revenue Loss  *(best business-impact story)*

- **Situation:** Outlets switched items off on Swiggy/Zomato when they ran out of stock. Nobody measured what that cost, because a sale that never happens leaves no row in the sales table.
- **Task:** Quantify the invisible loss and make it actionable per outlet.
- **Action:**
  - Realised the aggregator logs contained item on/off toggle events — that was the hidden data source.
  - Built a stockout history table: for every outlet × item, the time windows where the item was unavailable.
  - Built a baseline: average sales for that item, at that outlet, in that hour-of-day and day-of-week, from historical data — so a Tuesday 3pm stockout wasn't compared against a Saturday 8pm baseline.
  - Estimated loss = stockout hours × baseline hourly units × price, with a haircut for substitution (some customers buy an alternative rather than dropping off).
  - Ranked outlets and items by loss and sent a weekly list to ops leadership.
- **Result:** Estimated 10–12% of potential sales lost. Ops targeted the worst outlets and items; loss came down 2–4 percentage points over 2–3 months.
- **Learning:** The most valuable data is often data about what *didn't* happen.

> **They will ask: "How did you validate the 10–12%?"**
> Baseline built from same-outlet, same-hour, same-weekday history; sanity-checked against outlets with near-zero stockouts as a control; deliberately applied a substitution haircut so the estimate stayed conservative. Call it an estimate and state the assumptions — that is what makes you credible, not claiming precision.

### STAR 3 — Outlet Ranking & Scorecard  *(structuring and stakeholder story)*

- **Situation:** 140+ outlets, and "which outlets are doing well" was a matter of opinion — each function had its favourite metric.
- **Task:** Create one defensible performance score.
- **Action:**
  - Ran working sessions with sales, ops and CX to agree 15 parameters across three pillars: Sales (revenue, AOV, growth), Customer Experience (ratings, complaint rate, delivery time) and Operational Efficiency (stockouts, food cost %, wastage, order rejection).
  - Set thresholds from the actual distribution across outlets (percentile-based) rather than arbitrary targets, so the score was relative and achievable.
  - Normalised each parameter to a 0–10 sub-score, weighted the pillars, produced a single out-of-10 outlet score.
  - Built the Power BI view so a regional manager could see *why* an outlet scored low, not just the score.
- **Result:** Became the standard artefact in monthly business reviews. Consistent top performers' practices were replicated; bottom-decile outlets got targeted intervention.
- **Learning:** A score is only adopted if people can see how to move it. Transparency of sub-scores mattered more than sophistication of weighting.

> **They will ask: "How did you decide the weights?"**
> Business judgement agreed with stakeholders, sales weighted highest — then sensitivity-checked: I varied the weights and confirmed the top and bottom deciles barely moved, which told us the ranking was robust rather than an artefact of the weighting.

### STAR 4 — Food Cost & Margin Model  *(business-acumen story)*

- **Situation:** We knew revenue per outlet but not true profitability. Food cost was estimated at brand level, not outlet level.
- **Task:** Build an accurate outlet-level margin model.
- **Action:**
  - Consolidated three disconnected sources: the recipe master (item → raw-material quantities), raw-material purchase prices (which vary by city and month), and product-level sales.
  - Exploded sales into raw-material consumption via the recipe bill of materials, priced at the relevant period's rate → true food cost per product per outlet.
  - Layered packaging cost, aggregator commission, discount spend split into brand-funded vs platform-funded (very different, and often conflated), and taxes.
  - Output: contribution margin per item, per outlet, per channel.
- **Result:** Exposed products that were margin-negative once discounts and commission were counted — fed directly into pricing and menu decisions and outlet-level P&L conversations.
- **Learning:** Discounts hide inside revenue. Separating platform-funded from brand-funded discount changed the answer entirely for several items.

> **They will ask: "Gross margin vs contribution margin?"**
> Gross margin = revenue − COGS (food + packaging). Contribution margin also subtracts variable selling costs — commission, brand-funded discount, payment gateway. The second tells you whether an incremental order is worth taking.

### STAR 5 — Expired Items Tracking  *(initiative and integrity story)*

- **Situation:** Perishables were being consumed past expiry, and expiry dates were being entered incorrectly in the inventory system — sometimes carelessly, sometimes to mask wastage.
- **Task:** Nobody asked me to look at this. I noticed anomalies in batch consumption and raised it.
- **Action:**
  - Traced batch-level consumption against recipe requirements to find batches still being consumed after their expiry date.
  - Flagged data-entry patterns indicating incorrect expiry entry — expiry dates inconsistent with receipt date plus known shelf life.
  - Built a dashboard showing expired vs fresh consumption per outlet.
- **Result:** A quality and brand risk that was previously invisible became a monitored metric surfaced to leadership.
- **Learning:** Reporting an uncomfortable finding is part of the job. I framed it as a process gap rather than blame on specific outlets, which is why it got fixed instead of triggering defensiveness.

*(This is your "integrity / spoke up when it was awkward" story for behavioural rounds.)*

### STAR 6 — Stakeholder conflict / influencing without authority

- **Situation:** `[Fill in: a time ops or a regional manager disputed your numbers, or resisted the new dashboard]`
- **Task:** `[...]`
- **Action:** `[Likely shape: sat with them, reproduced their number, found the definition mismatch — gross vs net of discounts, or order date vs delivery date — documented the agreed definition, surfaced it as a tooltip in the dashboard]`
- **Result:** `[...]`
- **Learning:** Most "the data is wrong" complaints are definition mismatches, not data errors.

---

## D. Weak-spot answers to rehearse

**"Your Python looks light."**

> "Fair. My production work is SQL and DAX because that's what our stack runs on — BigQuery and Power BI. I use Python for ad-hoc analysis in Jupyter, mainly pandas for reshaping and matplotlib for quick checks. I've been deliberately closing that gap; ask me a pandas question and I'll work through it."

Then actually be able to. See [04_Python_and_Stats.md](04_Python_and_Stats.md).

**"Have you run A/B tests?"**

> "Not formal randomised experiments. Ours has been before/after with control outlets — like the stockout intervention, where I compared targeted outlets against a matched set. I know that's quasi-experimental and carries confounding risk. I understand the formal framework — hypothesis, power and sample size, primary metric, guardrail metrics — and running experiments properly is the main thing I want from my next role."

Never claim an A/B test you didn't run. They will ask about sample-size calculation and you will be caught.

**"You've only worked at one company."**

> "One company, but four quite different problems — cost engineering, revenue leakage, performance measurement and unit economics. And I've watched the function grow from spreadsheets to a governed BI platform, which you usually only get in a startup."

**"Engineering Physics — why analytics?"**

> "Physics is applied modelling: take a messy real system, decide which effects matter, build a simplified model that predicts well enough to act on. That's exactly what the margin model is. The degree gave me the quantitative comfort; the business interest came from my internships."

**"You're an Assistant Manager applying for an individual-contributor senior analyst role — is that a step down?"**

> "Titles vary a lot by company size. My day-to-day is already senior-IC work with some ownership of the function. I care about scope and the problems, not the label — and I'd rather earn a manager title in a bigger org than carry one in a smaller one."

---

## E. Numbers to memorise

| Fact | Number |
|---|---|
| Outlets analysed | 140+ |
| Parameters in scorecard | 15 |
| Query consumption reduction | 35–40% |
| Monthly saving | ₹1.5–2 lakh |
| Estimated sales loss from stockouts | 10–12% |
| Loss recovered | 2–4 pp in 2–3 months |
| Tenure at Burger Singh | Jan 2022 – present (~4 yr 8 mo as of Sep 2026) |
| Education | IIT Delhi, B.Tech Engineering Physics, 2019 |

Interviewers use scale questions to calibrate you. Write your real answers here before any interview:

- Approx. annual revenue of the business: `[...]`
- Largest table row count / daily data volume: `[...]`
- Number of dashboard users: `[...]`
- People managed or mentored: `[...]`
- Number of dashboards/reports you own: `[...]`
