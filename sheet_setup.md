# Google Sheet Setup

Create a Google Sheet with two tabs.

## Tab 1: `raw_data`
Paste the contents of `sample_data/ad_performance.csv` (or your real export) starting at A1.

| A | B | C | D | E | F | G | H |
|---|---|---|---|---|---|---|---|
| week_start | platform | campaign | spend | impressions | clicks | conversions | revenue |

## Tab 2: `report_input`
This tab is what Make.com reads. One row per campaign for the latest week, with the previous week beside it.

Put these headers in row 1:

| A | B | C | D | E | F | G | H | I | J | K | L | M |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| platform | campaign | spend | clicks | conversions | revenue | CTR | CPC | CVR | CPA | ROAS | ROAS_WoW | CPA_WoW |

Set `Z1` to the latest week, e.g. `=MAX(raw_data!A2:A)`, and `Z2` to the previous week, e.g. `=Z1-7`.

Fill A2:B with your campaign list, then use these formulas in row 2 and drag down:

```
C2  =SUMIFS(raw_data!$D:$D, raw_data!$C:$C, $B2, raw_data!$A:$A, $Z$1)
D2  =SUMIFS(raw_data!$F:$F, raw_data!$C:$C, $B2, raw_data!$A:$A, $Z$1)
E2  =SUMIFS(raw_data!$G:$G, raw_data!$C:$C, $B2, raw_data!$A:$A, $Z$1)
F2  =SUMIFS(raw_data!$H:$H, raw_data!$C:$C, $B2, raw_data!$A:$A, $Z$1)

G2  =IFERROR(D2 / SUMIFS(raw_data!$E:$E, raw_data!$C:$C, $B2, raw_data!$A:$A, $Z$1), 0)
H2  =IFERROR(C2 / D2, 0)
I2  =IFERROR(E2 / D2, 0)
J2  =IFERROR(C2 / E2, 0)
K2  =IFERROR(F2 / C2, 0)

L2  =IFERROR(K2 / (SUMIFS(raw_data!$H:$H, raw_data!$C:$C, $B2, raw_data!$A:$A, $Z$2) / SUMIFS(raw_data!$D:$D, raw_data!$C:$C, $B2, raw_data!$A:$A, $Z$2)) - 1, 0)
M2  =IFERROR(J2 / (SUMIFS(raw_data!$D:$D, raw_data!$C:$C, $B2, raw_data!$A:$A, $Z$2) / SUMIFS(raw_data!$G:$G, raw_data!$C:$C, $B2, raw_data!$A:$A, $Z$2)) - 1, 0)
```

Format G, I, L, M as percentages, and K as `0.00"x"`.

## Why the metrics are computed in the Sheet and not by the AI
Language models make arithmetic mistakes. The Sheet does the maths, so the AI only interprets numbers that are already correct. This is a deliberate design choice and worth mentioning in an interview.
