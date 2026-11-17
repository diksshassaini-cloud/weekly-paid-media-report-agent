# Weekly Paid Media Report Agent

An AI agent built in Make.com (no code) that turns raw weekly ad data into a client-ready performance report, flags the campaigns that need action, and emails the result every Monday.

> **Status:** replace the placeholders marked `TODO` with your own screenshots and results after you run it.

## The problem
Agencies spend hours every week pulling numbers from Meta and Google, calculating ROAS and CPA, spotting what broke, and writing the client email. It is repetitive, and the analysis is often rushed or skipped.

## What it does
1. Reads weekly campaign data from a Google Sheet.
2. Calculates CTR, CPC, CVR, CPA, ROAS and week-on-week change **in the Sheet**, so the maths is exact.
3. Sends the numbers to an AI model with a strict analyst prompt (rules for what counts as a problem or a winner).
4. Emails a formatted HTML report: headline, campaign status table (winner / problem / watch), what needs attention (cause, first check, action), what is working, and a next-week plan.
5. Optional: sends an instant alert if any campaign's ROAS drops more than 15%.

## Architecture
```
Schedule (Mon 9 AM)
   -> Google Sheets (search rows, pre-calculated metrics)
   -> Text Aggregator (all campaigns in one payload)
   -> Gemini AI (analyst prompt, temperature 0.2)
   -> Gmail (send report)
```

## Key design decisions
- **Maths in the Sheet, judgement in the AI.** Language models can get arithmetic wrong, so they only interpret numbers that are already correct.
- **Explicit rules in the prompt.** "Problem" and "winner" are defined with thresholds (for example ROAS down more than 15% WoW), so output is consistent week to week.
- **Hypotheses, not claims.** The prompt forces the AI to say "likely creative fatigue" and what to check first, because data alone cannot prove a cause.
- **Low temperature (0.2)** for repeatable reports.

## Sample data
`sample_data/ad_performance.csv` is **made-up demo data** for 5 campaigns across 4 weeks. It is deliberately built with two issues for the agent to catch:

| Campaign | Latest week vs previous | What the agent should flag |
|---|---|---|
| Prospecting - Interest (Meta) | ROAS 0.76 vs 1.07 (-30%), CPA +42%, CTR falling for 3 weeks | Likely creative or audience fatigue |
| Search - Generic (Google) | ROAS 0.98 vs 2.06 (-52%), spend +33%, CPA +110% | Spend jump with falling conversions, check search terms and match types |
| Retargeting - Cart Abandoners (Meta) | ROAS 4.05, stable | Winner, room to scale |
| Search - Brand (Google) | ROAS 7.02, stable | Winner, but capped by brand demand |

Account total for the latest week: spend ₹42,300, revenue ₹89,900, ROAS 2.13x.

## How to set it up
1. Create the Google Sheet: [`docs/sheet_setup.md`](docs/sheet_setup.md)
2. Build the Make scenario: [`docs/make_scenario_setup.md`](docs/make_scenario_setup.md)
3. Paste the prompt from [`prompts/report_analyst_prompt.md`](prompts/report_analyst_prompt.md) into the AI module.

## Results
TODO: add a screenshot of the Make scenario canvas.
TODO: add a screenshot of the generated email.
TODO: add one line on time saved, e.g. "replaces about 2 hours of manual weekly reporting per client" (only if you can justify it).

## Tech
Make.com, Google Sheets, Google Gemini API, Gmail.

## Limitations and next steps
- Uses sample data. The next step is connecting live Meta and Google Ads exports (Windsor.ai or a scheduled CSV export into the Sheet).
- Thresholds are fixed. They could be made per-client in a settings tab.
- No benchmark comparison yet (industry or client-specific targets).

## Author
Diksha Saini
