---
name: demo-1a-transcript-downloader
description: Downloads earnings call transcripts from the Financial Modeling Prep API and saves them as a local text file. Use when user says "download transcript", "get earnings call", "fetch transcript for NVDA", or mentions a ticker and quarter like "NVDA Q1 2024". Do NOT use for transcript analysis or summarization (use demo-1b or demo-1c instead).
---

# Transcript Downloader

**Demo 1A** — A simple, reusable skill. Downloads a transcript and saves it. Multiple agents depend on this.

## Instructions

### Step 1: Load API Key
1. Read `.env` from the project root
2. Extract `FMP_API_KEY`
3. If `.env` is missing, ask the user for the FMP API key

### Step 2: Parse Arguments
- `$0` = ticker symbol (e.g., `NVDA`)
- `$1` = year (e.g., `2024`)
- `$2` = quarter (e.g., `Q1` or `1`)
- CRITICAL: Strip the "Q" prefix — API expects just the digit

### Step 3: Fetch Transcript
Run:
```bash
curl -s "https://financialmodelingprep.com/stable/earning-call-transcript?symbol={TICKER}&quarter={Q_NUM}&year={YEAR}&apikey={FMP_API_KEY}"
```
- Response is a JSON array
- Extract the `content` field from the first element
- If empty array or error, tell the user the transcript was not found

### Step 4: Save
Write the transcript text to `transcripts/{TICKER}_{YEAR}_Q{NUM}.txt`

### Step 5: Report
Tell the user:
- Ticker, year, quarter
- Word count
- File path
- First 200 characters as preview

## Examples

**Example 1:** User says "Download the NVDA Q1 2024 transcript"
- Calls FMP API with symbol=NVDA, quarter=1, year=2024
- Saves to `transcripts/NVDA_2024_Q1.txt`
- Reports: "NVDA Q1 2024 — 9,004 words saved"

**Example 2:** User says "Get earnings call for AAPL 2023 Q3"
- Calls FMP API with symbol=AAPL, quarter=3, year=2023
- Saves to `transcripts/AAPL_2023_Q3.txt`

## Common Issues

### "Legacy Endpoint" error
You used the old API URL. Use the **stable** endpoint:
`https://financialmodelingprep.com/stable/earning-call-transcript`

### Empty response
The ticker or quarter may not exist in FMP. Check the ticker symbol and try adjacent quarters.
