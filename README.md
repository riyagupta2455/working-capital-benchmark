# Working Capital Benchmarking: Any Industry, Straight from SEC Filings

**How much cash is stuck in the cash conversion cycle, and how much could a laggard free up by matching its peers?**

A Python / Google Colab tool that pulls 10 years of audited 10-K data straight from SEC EDGAR, benchmarks working-capital efficiency across a peer group in **any industry**, and sizes the cash-release opportunity for an underperformer. The worked case study below is US packaged food & beverage.

[Open the notebook](working_capital_benchmark.ipynb) · Runs end to end in about a minute in Colab, no API key needed.

---

## Why this matters

Working capital is the cheapest source of funding a company has. Every day taken out of the cash conversion cycle (CCC) frees cash for debt paydown, buybacks or capex, with no new financing. It's a standard lens for CFOs, FP&A, treasury, strategy consultants and equity analysts.

| Metric | Formula | What it tells you |
|---|---|---|
| DSO | avg receivables / revenue × 365 | how long customers take to pay |
| DIO | avg inventory / COGS × 365 | how long stock sits before it's sold |
| DPO | avg payables / COGS × 365 | how long the company takes to pay suppliers |
| **CCC** | DSO + DIO − DPO | days of cash tied up in operations |

A negative CCC means suppliers are effectively funding part of the business.

## Use it for any industry

Pick a preset from the dropdown in the config cell, or choose `Custom` and type any US-listed tickers:

| Preset | Peers |
|---|---|
| Packaged food & beverage | PEP, KO, MDLZ, KDP, GIS, HSY, KHC, CAG, CPB, SJM, HRL |
| Household & personal care | PG, CL, KMB, CHD, CLX, EL, COTY |
| Big-box & grocery retail | WMT, TGT, COST, KR, DG, DLTR, BJ, ACI |
| Apparel & footwear | NKE, LULU, TJX, ROST, GAP, PVH, RL, VFC, DECK |
| Home improvement & specialty retail | HD, LOW, BBY, TSCO, WSM, ULTA |
| Pharma & biotech | PFE, MRK, JNJ, ABBV, LLY, BMY, AMGN, GILD |
| Semiconductors | NVDA, AMD, INTC, QCOM, TXN, AVGO, MU, ADI |
| Tech hardware | AAPL, DELL, HPQ, HPE, CSCO, ANET, SMCI |
| Software | MSFT, ORCL, CRM, ADBE, NOW, INTU, WDAY, ADSK |
| Industrial machinery | CAT, DE, CMI, PCAR, ETN, PH, EMR, DOV |
| Autos | GM, F, TSLA |
| Airlines | DAL, UAL, AAL, LUV, ALK, JBLU |

Leave `TARGET` blank and the notebook automatically sizes the opportunity for the peer group's laggard (the company with the longest cash cycle).

## Case study: US packaged food & beverage (FY2025)

- **Wide spread across the peer group:** CCC ranges from about **−38 days (Mondelez)** to about **+57 days (Hormel)**. The peer median is about **11 days** (DSO 26, DIO 71, DPO 93).
- **4 of 11 companies run a negative CCC:** Mondelez, Keurig Dr Pepper, General Mills and PepsiCo. All four stretch payables well beyond 90 days.
- **Kraft Heinz has slipped the most:** its CCC went from about −19 days in 2017 to +11 in 2025, a **30-day** deterioration and the biggest in the group. Campbell's improved the most, taking out about 41 days.
- **Cash opportunity at Kraft Heinz:** matching the peer median would free about **$684m** of cash:
  - Receivables (collect faster): ~$463m
  - Inventory (hold less): ~$221m
  - Payables: $0, because KHC already pays suppliers more slowly than the median, so there's no gap to close

![Cash release waterfall](images/waterfall.png)

![CCC heatmap](images/ccc_heatmap.png)

![CCC build-up](images/ccc_buildup.png)

## Seasonality: the quarterly view

Year-end balance sheets show the quietest day of the year. Quarterly 10-Q data shows the peak, which is what treasury actually has to fund.

- **Hershey** ties up the most cash in Q2 (CCC ~52 days vs ~35 in Q4), consistent with building inventory ahead of Halloween.
- **Keurig Dr Pepper** has the biggest seasonal swing (~18 days) but stays deeply negative all year (around −80 to −100 days), thanks to long supplier terms.
- **Kraft Heinz** is the least seasonal (CCC 8–11 days every quarter). Its trade working capital still moves by about **$400m** within a typical year, peaking in Q1.
- Coca-Cola and PepsiCo are left out of the quarterly view: their 10-Qs report payables together with accrued liabilities, which would make the swing look far bigger than it is.

![Seasonality by fiscal quarter](images/seasonality.png)

![Kraft Heinz quarterly working-capital days](images/target_quarterly.png)

## How it works

1. **Data:** SEC EDGAR XBRL `companyfacts` API (free, primary-source 10-K data). Tickers are mapped to CIKs automatically.
2. **Cleaning:** companies tag the same line item differently (e.g. `AccountsReceivableNetCurrent` vs `AccountsAndOtherReceivablesNetCurrent`), so each item uses a ranked list of XBRL tags. Annual flows are kept only for ~12-month periods, and restated values from later filings take priority.
3. **Industry edge cases:**
   - Companies with no inventory (software) get DIO = 0 instead of being dropped.
   - LIFO reporters (e.g. Kroger) get inventory derived as FIFO cost minus LIFO reserve.
   - Cash-and-card retailers that rarely report receivables have those treated as 0.
   - Companies with no cost-of-sales line (airlines) use total operating costs as a flagged proxy.
4. **Fiscal-year alignment:** each fiscal year is labelled by the calendar year most of it falls in, so Walmart (January year-end) and General Mills (May) line up with December filers.
5. **Data quality:** payables reported together with accrued liabilities (which overstates DPO) are excluded, and a tag log shows which tag fed every number.
6. **Metrics:** DSO / DIO / DPO / CCC on average balances, which smooths out year-end window dressing.
7. **Benchmarking:** peer median and top quartile, a heatmap over time, and target vs peer interquartile range.
8. **Opportunity sizing and sensitivity:** day gaps converted to dollars, lever by lever, plus cash freed per day of improvement.
9. **Seasonality:** Q1–Q3 from 10-Qs, Q4 balances from the 10-K and Q4 flows as full year minus Q1–Q3, labelled by fiscal quarter.
10. **Export:** everything goes to `working_capital_benchmark.xlsx`.

## Limitations

- **Tags aren't always comparable:** some receivables lines include non-trade items.
- **Supply-chain finance** (reverse factoring) inflates DPO (Keurig Dr Pepper is an extreme case) and is only partly disclosed.
- **Receivables factoring or securitisation** lowers DSO without any change in customer behaviour.
- **Industries differ structurally:** only compare within a peer group. For software, DPO is measured against a small cost base and is noisy, so focus on DSO there.
- **M&A distorts averages:** deals like KDP's merger and KHC's divestitures cause step changes.
- **Quarterly data is rougher:** Q4 flows are derived and quarter-end (not average) balances are used.
- **Benchmarking isn't target-setting:** channel mix and geography can justify a different cycle.
- **US filers only:** companies that don't file 10-Ks with the SEC (e.g. most Indian or European listings) aren't covered.

## Run it yourself

1. Open the notebook in [Google Colab](https://colab.research.google.com/) (File → Upload notebook, or open it from GitHub).
2. Set `USER_AGENT` to your name and email. SEC requires this for API access.
3. Pick an industry (or Custom tickers), then Runtime → Run all.

## Tools

Python · pandas · NumPy · matplotlib · seaborn · requests · SEC EDGAR XBRL API · Google Colab
