---
name: demo-1c-prompt-analysis
description: Runs custom analysis prompts from a YAML file against an earnings transcript. Prompts are defined per company in prompts/{TICKER}.yaml — management can edit these without code changes. Use when user says "run custom analysis", "prompt analysis", "run portfolio prompts for NVDA", or "gdoc analysis". Requires a transcript from demo-1a. Do NOT use for simple hardcoded analysis (use demo-1b instead).
---

# Custom Prompt Transcript Analysis

**Demo 1C** — Human-augmented analysis. Management defines the questions, the agent finds the answers.

## Instructions

### Step 1: Load Inputs
1. Read `transcripts/{TICKER}_{YEAR}_Q{NUM}.txt` — if missing, tell user to run `/demo-1a-transcript-downloader`
2. Read `prompts/{TICKER}.yaml` — if missing, check for `prompts/default.yaml` — if neither exists, tell user no prompts configured

**YAML format:**
```yaml
company: NVIDIA
ticker: NVDA
prompts:
  - "Value chain changes in the last quarter"
  - "Current and end market growth trajectory"
```

### Step 2: Analyze Each Prompt
For EACH prompt in the YAML file, produce:
- **Finding:** 2-4 sentence evidence-based answer
- **Key quotes:** 1-2 direct quotes with speaker name
- **Signal:** POSITIVE / NEUTRAL / NEGATIVE / MIXED
- **Confidence:** HIGH / MEDIUM / LOW

## Important
- Analyze each prompt independently against the full transcript
- If the transcript doesn't address a prompt, say so and mark confidence LOW
- Never speculate beyond what management said

### Step 3: Compile Report
Create markdown with this structure:

```markdown
# {COMPANY} ({TICKER}) — Q{NUM} {YEAR} Transcript Analysis

**Analysis date:** {today}
**Prompts source:** prompts/{TICKER}.yaml
**Number of prompts:** {count}

---

## 1. {Prompt text}
**Signal:** POSITIVE | **Confidence:** HIGH
{Finding}
> "{Quote}" — {Speaker}

---
## Executive Summary
{2-3 sentence synthesis}

| Prompt | Signal | Confidence |
|--------|--------|------------|
```

### Step 4: Save
1. Write to `analyses/{TICKER}_{YEAR}_Q{NUM}_gdoc.md`
2. Copy to `{GBLAI_GDRIVE_PATH}/analyses/`

### Step 5: Report
Show the executive summary table and file locations.

## Examples

**Example 1:** User says "Run portfolio prompts for NVDA Q1 2024"
- Reads 6 prompts from `prompts/NVDA.yaml`
- Produces 6-section markdown report
- Each section has signal + confidence + finding + quotes

## Common Issues

### No prompt file found
Create `prompts/{TICKER}.yaml` with the company name and a list of prompts. See `prompts/NVDA.yaml` for an example.
