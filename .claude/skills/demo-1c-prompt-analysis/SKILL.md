---
name: demo-1c-prompt-analysis
description: Runs custom analysis prompts from an Excel file against an earnings transcript. Prompts are stored in prompts/portfolio_prompts.xlsx with columns for Company, Ticker, Prompt, and Frequency. Management can edit the Excel file without code changes. Use when user says "run custom analysis", "prompt analysis", "run portfolio prompts for NVDA", or "gdoc analysis". Requires a transcript from demo-1a. Do NOT use for simple hardcoded analysis (use demo-1b instead).
---

# Custom Prompt Transcript Analysis

**Demo 1C** — Human-augmented analysis. Management defines the questions in Excel, the agent finds the answers.

## Instructions

### Step 1: Load Inputs
1. Read `transcripts/{TICKER}_{YEAR}_Q{NUM}.txt` — if missing, tell user to run `/demo-1a-transcript-downloader`
2. Read `prompts/portfolio_prompts.xlsx` — filter rows where Ticker column matches the requested ticker
3. If no matching rows, tell the user no prompts are configured for that ticker

**Excel format (prompts/portfolio_prompts.xlsx):**

| Company | Ticker | Prompt | Frequency |
|---------|--------|--------|-----------|
| NVIDIA | NVDA | Value chain changes in the last quarter | Every quarter |
| NVIDIA | NVDA | Management tone on capital allocation | Every quarter |
| SGS Group | SGSN.SW | Organic revenue growth by division | Every quarter |

**Reading the Excel file:**
```bash
python3 -c "
import openpyxl
wb = openpyxl.load_workbook('prompts/portfolio_prompts.xlsx')
ws = wb.active
rows = [dict(zip(['company','ticker','prompt','frequency'], [c.value for c in row])) for row in ws.iter_rows(min_row=2)]
ticker_rows = [r for r in rows if r['ticker'] == '{TICKER}']
for r in ticker_rows:
    print(r['prompt'])
"
```

### Step 2: Analyze Each Prompt
For EACH prompt matching the ticker, produce:
- **Finding:** 2-4 sentence evidence-based answer
- **Key quotes:** 1-2 direct quotes with speaker name
- **Signal:** POSITIVE / NEUTRAL / NEGATIVE / MIXED
- **Confidence:** HIGH / MEDIUM / LOW

## Important
- Analyze each prompt independently against the full transcript
- If the transcript doesn't address a prompt, say so and mark confidence LOW
- Never speculate beyond what management said
- Note the Frequency column in the report header but analyze all matching prompts regardless

### Step 3: Compile Report
Create markdown with this structure:

```markdown
# {COMPANY} ({TICKER}) — Q{NUM} {YEAR} Transcript Analysis

**Analysis date:** {today}
**Prompts source:** prompts/portfolio_prompts.xlsx
**Number of prompts:** {count}

---

## 1. {Prompt text}
**Signal:** POSITIVE | **Confidence:** HIGH | **Frequency:** Every quarter
{Finding}
> "{Quote}" — {Speaker}

---
## Executive Summary
{2-3 sentence synthesis}

| Prompt | Signal | Confidence | Frequency |
|--------|--------|------------|-----------|
```

### Step 4: Save
1. Write to `analyses/{TICKER}_{YEAR}_Q{NUM}_gdoc.md`
2. Copy to `{GBLAI_GDRIVE_PATH}/analyses/` (read path from `.env`)

### Step 5: Report
Show the executive summary table and file locations.

## Examples

**Example 1:** User says "Run portfolio prompts for NVDA Q1 2024"
- Reads prompts/portfolio_prompts.xlsx, filters for NVDA (6 rows)
- Produces 6-section markdown report
- Each section has signal + confidence + frequency + finding + quotes

## Common Issues

### No prompts found for ticker
Open `prompts/portfolio_prompts.xlsx` and add rows with the ticker. Columns: Company, Ticker, Prompt, Frequency.

### openpyxl not installed
Run: `pip3 install openpyxl`
