# Make.com Scenario Setup

Scenario name: **Weekly Paid Media Report Agent**

Build five modules in this order.

## 1. Schedule (trigger)
- Module: **Tools > Schedule** (or the clock icon on the scenario)
- Run: every **Monday, 9:00 AM IST**

## 2. Google Sheets > Search Rows
- Spreadsheet: your sheet
- Sheet: `report_input`
- Filter: `campaign` **Exists**
- Limit: 50
- Output: one bundle per campaign with the pre-calculated metrics

## 3. Tools > Text Aggregator
- Source module: Search Rows
- Text template, one line per campaign (map each field from the panel):
  `campaign: [campaign] | platform: [platform] | spend: [spend] | clicks: [clicks] | conversions: [conversions] | revenue: [revenue] | CTR: [CTR] | CPC: [CPC] | CVR: [CVR] | CPA: [CPA] | ROAS: [ROAS] | ROAS_WoW: [ROAS_WoW] | CPA_WoW: [CPA_WoW]`
- Row separator: New row
- This turns all campaign rows into plain text, so the AI sees the whole account in a single call. (An Array Aggregator maps messily into AI modules, which is why a Text Aggregator is used.)

## 4. Google Gemini AI > Generate a Response (or OpenAI > Create a Completion)
- Model: Gemini 2.5 Flash (free tier is enough) or GPT-4o-mini
- System / instructions: paste the full text of `prompts/report_analyst_prompt.md`
- User message: `Metrics for the latest week:` followed by the Text Aggregator output
- Temperature: **0.2** (low = consistent, factual reports)

## 5. Gmail > Send an Email
- To: your own address while testing (switch to the client later)
- Subject: `Weekly Paid Media Report`
- Content type: HTML
- Body: map the AI output (the prompt makes the AI return a ready-to-send HTML email)

## Optional extras (each is a good talking point)
- **Slack/WhatsApp alert**: add a Router after the AI step. If any campaign has `ROAS_WoW` below -15%, send an instant alert to the account manager.
- **Log to Sheet**: add a Google Sheets > Add a Row module that saves every generated report to a `report_log` tab so there is a history.
- **Error handling**: right-click the AI module > Add error handler > Resume, with a fallback email saying "Report failed, check scenario".

## Testing checklist
1. Click **Run once** with the sample data.
2. Confirm Search Rows returns 5 bundles (if it returns 0, remove any filter on the module).
3. Confirm the AI response flags **Prospecting - Interest** and **Search - Generic** as problems.
4. Confirm the email arrives and the numbers match the Sheet.
5. Screenshot the scenario canvas and a sample email for the README.
