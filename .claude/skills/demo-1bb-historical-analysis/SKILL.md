---
name: demo-1bb-historical-analysis
description: Runs /demo-1a-transcript-downloader and /demo-1b-simple-analysis across the last 12 quarters for a ticker, all in parallel, then generates an interactive HTML matrix with color-coded scores and hover rationales. Use when user says "historical analysis", "multi-quarter analysis", "12 quarter view", "trend analysis for NVDA", or "run historical transcript analysis". Requires FMP API key in .env.
---

# Historical Transcript Analysis

**Demo 1BB** — Run 12 agents in parallel across 3 years of earnings calls. Generate a visual scorecard matrix.

## Instructions

### Step 1: Determine Quarters
- `$0` = ticker (e.g., `NVDA`)
- Default: last 12 quarters = Q1-Q4 for years {current_year - 2}, {current_year - 1}, {current_year}
- User can optionally specify a range

### Step 2: Download All Transcripts (Parallel)
Launch 12 parallel instances of the transcript downloader:
```
For each quarter in the 12-quarter range:
  - Call FMP API: https://financialmodelingprep.com/stable/earning-call-transcript?symbol={TICKER}&quarter={Q}&year={Y}&apikey={KEY}
  - Save to transcripts/{TICKER}_{YEAR}_Q{NUM}.txt
```
Run these in parallel using the Agent tool — all 12 can run simultaneously.

### Step 3: Analyze All Transcripts (Parallel)
Launch 12 parallel analysis agents. For each transcript, produce the simple analysis JSON with:
- `management_confidence_score` (1-10)
- `guidance_credibility` (1-10)
- `thesis_alignment_score` (1-10)
- `top_3_risks` with severity and trend
- `capital_allocation_signals`
- `notable_quotes`

Save each to `analyses/{TICKER}_{YEAR}_Q{NUM}_simple.json`

### Step 4: Generate HTML Matrix
Create `analyses/{TICKER}_historical_matrix.html` with:

**Layout:**
- Columns = quarters (chronological, left to right)
- Rows = scored metrics (management confidence, guidance credibility, thesis alignment, risk count by severity)
- Each cell is color-coded: red (1-3) → amber (4-6) → green (7-10)
- Hovering over a cell shows the rationale text

## Important: All scores are 1-10 integers
Every metric in the matrix uses a 1-10 numerical scale. No text labels like HIGH/MEDIUM/LOW. This includes guidance credibility (1=missed badly, 10=beat-and-raise consistently).

**Color scale (CSS classes `score-1` through `score-10`):**
- Score 1-3: `#DC2626` (red)
- Score 4-5: `#D97706` (amber)
- Score 6: `#65A30D` (lime)
- Score 7: `#16A34A` (green)
- Score 8: `#15803D` (dark green)
- Score 9-10: `#166534` (deepest green)

**Interactive features:**
- Clicking a cell opens a modal overlay showing the metric name, quarter, score, and full rationale text
- Modal closes on click outside, close button, or Escape key
- Do NOT use CSS hover tooltips (they get clipped by overflow containers and iframes)
- Header row shows quarter labels (Q1 2022, Q2 2022, etc.)
- Row labels on the left for each metric
- Notable quotes section below the matrix

### Step 5: Report
- Tell the user the matrix is saved
- Open the HTML file in the browser
- Summarize any notable trends (improving/declining scores)

## Examples

**Example 1:** User says "Run historical analysis for NVDA"
- Downloads Q1 2022 through Q4 2024 (12 transcripts)
- Runs 12 analyses in parallel
- Generates `analyses/NVDA_historical_matrix.html`
- Opens in browser showing color-coded scorecard

## Common Issues

### Some quarters missing
FMP may not have all quarters. Skip missing ones and note gaps in the matrix.

### Analysis consistency
Running 12 analyses in parallel may produce slightly different scoring styles. This is expected — the matrix shows relative trends, not absolute precision.
