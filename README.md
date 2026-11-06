# Working Capital Benchmarking: US Packaged Food & Beverage

**How much cash is stuck in the cash conversion cycle, and how much could a laggard free up by matching its peers?**

A Python / Google Colab project that pulls 10 years of audited 10-K data straight from SEC EDGAR, benchmarks working-capital efficiency across 11 US packaged food & beverage companies, and sizes the cash-release opportunity for an underperformer.

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

## Peer set

PepsiCo (PEP), Coca-Cola (KO), Mondelez (MDLZ), Keurig Dr Pepper (KDP), General Mills (GIS), Hershey (HSY), Kraft Heinz (KHC), Conagra (CAG), Campbell's (CPB), J.M. Smucker (SJM), Hormel (HRL)

## Key findings (FY2025)

- **Wide spread across the peer group:** CCC ranges from about **−38 days (Mondelez)** to about **+57 days (Hormel)**. The peer median is about **11 days** (DSO 28, DIO 72, DPO 93).
- **4 of 11 companies run a negative CCC:** Mondelez, Keurig Dr Pepper, General Mills and PepsiCo. All four stretch payables well beyond 90 days.
- **Kraft Heinz has slipped the most:** its CCC is up about **30 days** over the window, the biggest deterioration in the group. Smucker's improved the most, taking out about 41 days.
- **Cash opportunity at Kraft Heinz:** matching the peer median would free about **$548m** of cash:
  - Receivables (collect faster): ~$350m
  - Inventory (hold less): ~$198m
  - Payables: $0, because KHC already pays suppliers more slowly than the median, so there's no gap to close

![Cash release waterfall](images/waterfall.png)

![CCC heatmap](images/ccc_heatmap.png)

![CCC build-up](images/ccc_buildup.png)

## How it works

1. **Data:** SEC EDGAR XBRL `companyfacts` API (free, primary-source 10-K data). Tickers are mapped to CIKs automatically.
2. **Cleaning:** companies tag the same line item differently (e.g. `AccountsReceivableNetCurrent` vs `AccountsNotesAndLoansReceivableNetCurrent`), so each item uses a ranked list of XBRL tags. Annual flows are kept only for ~12-month periods, and restated values from later filings take priority.
3. **Data quality:** when payables are reported together with accrued liabilities, which overstates DPO, those company-years are excluded rather than silently trusted. A tag log shows which tag fed every number.
4. **Metrics:** DSO / DIO / DPO / CCC on average balances, which smooths out year-end window dressing.
5. **Benchmarking:** peer median and top quartile, a heatmap over time, and target vs peer interquartile range.
6. **Opportunity sizing:** day gaps converted to dollars, lever by lever, for any target company.
7. **Sensitivity:** cash freed per day of improvement on each lever.
8. **Export:** everything goes to `working_capital_benchmark.xlsx`.

To reuse it, change `TARGET`, `PEERS` or `BENCHMARK` in the config cell. For example, set `PEERS = ["WMT","TGT","COST","KR","DG"]` to look at retail.

## Limitations

- **Tags aren't always comparable:** some receivables lines include non-trade items.
- **Supply-chain finance** (reverse factoring) inflates DPO and is only partly disclosed.
- **Receivables factoring or securitisation** lowers DSO without any change in customer behaviour.
- **Fiscal year-ends differ:** General Mills ends in May, most others in December, so seasonal inventory peaks don't line up.
- **M&A distorts averages:** deals like KDP's merger and KHC's divestitures cause step changes.
- **Benchmarking isn't target-setting:** channel mix and geography can justify a different cycle.

## Run it yourself

1. Open the notebook in [Google Colab](https://colab.research.google.com/) (File → Upload notebook, or open it from GitHub).
2. Set `USER_AGENT` to your name and email. SEC requires this for API access.
3. Runtime → Run all.

## Tools

Python · pandas · NumPy · matplotlib · seaborn · requests · SEC EDGAR XBRL API · Google Colab
