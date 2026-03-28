---
name: demo-1b-simple-analysis
description: Runs a hardcoded qualitative screen on an earnings transcript and outputs structured JSON. Use when user says "analyze transcript", "run qualitative screen", "simple analysis of NVDA", or "quick earnings analysis". Requires a transcript file from demo-1a-transcript-downloader. Do NOT use for custom prompt analysis (use demo-1c instead).
---

# Simple Transcript Analysis

**Demo 1B** — One agent, one transcript, hardcoded screen. The "Hello World" of investment agents.

## Instructions

### Step 1: Load Transcript
1. Read `transcripts/{TICKER}_{YEAR}_Q{NUM}.txt`
2. If not found, tell the user to run `/demo-1a-transcript-downloader` first

### Step 2: Analyze
Produce a JSON object with ALL of these fields:

```json
{
  "ticker": "NVDA",
  "year": 2024,
  "quarter": "Q1",
  "analysis_date": "YYYY-MM-DD",
  "value_chain_changes": "2-3 sentences",
  "end_market_growth": "2-3 sentences per segment",
  "management_confidence_score": 8,
  "management_confidence_rationale": "1-2 sentences",
  "capital_allocation_signals": {
    "ma_stance": "...",
    "capex": "...",
    "buybacks": "..."
  },
  "guidance_credibility": "HIGH|MEDIUM|LOW",
  "guidance_credibility_rationale": "1-2 sentences",
  "top_3_risks": [
    {"risk": "...", "severity": "HIGH|MEDIUM|LOW", "trend": "INCREASING|STABLE|DECREASING"}
  ],
  "notable_quotes": [
    {"speaker": "...", "quote": "...", "context": "..."}
  ],
  "thesis_alignment_score": 7,
  "thesis_alignment_rationale": "..."
}
```

## Important
- `management_confidence_score`: 1 = deeply pessimistic, 10 = extremely bullish
- `thesis_alignment`: assumes growth/quality thesis (strong moat, pricing power, secular tailwinds)
- Be evidence-based — cite what management actually said
- Pick quotes that would surprise an investment professional

### Step 3: Save
1. Write JSON to `analyses/{TICKER}_{YEAR}_Q{NUM}_simple.json`
2. Copy to `{GBLAI_GDRIVE_PATH}/analyses/` (read path from `.env`)

### Step 4: Report
Show formatted summary: confidence score, guidance credibility, top 3 risks, thesis alignment, 1-2 quotes.

## Examples

**Example 1:** User says "Run a quick analysis on NVDA Q1 2024"
- Reads `transcripts/NVDA_2024_Q1.txt`
- Produces JSON with confidence=9, credibility=HIGH
- Saves to `analyses/NVDA_2024_Q1_simple.json`

## Common Issues

### Missing transcript
User needs to run `/demo-1a-transcript-downloader` first. This skill does not fetch transcripts.
