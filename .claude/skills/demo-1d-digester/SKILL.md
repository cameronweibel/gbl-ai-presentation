---
name: demo-1d-digester
description: Creates role-specific digests of a transcript analysis, tailored for analyst, associate, VP, and partner seniority levels. Same data, four noise levels. Use when user says "digest analysis", "create digests", "tailor for partner", "per-level summaries", or "summarize for the team". Requires analysis output from demo-1b or demo-1c. Do NOT use for raw transcript analysis (use demo-1b or demo-1c first).
---

# AI Digester

**Demo 1D** — Same analysis, four audiences. An analyst needs detail; a partner needs a verdict.

## Instructions

### Step 1: Load Analysis
1. Try `analyses/{TICKER}_{YEAR}_Q{NUM}_gdoc.md` first (preferred)
2. Fall back to `analyses/{TICKER}_{YEAR}_Q{NUM}_simple.json`
3. If neither exists, tell user to run `/demo-1b-simple-analysis` or `/demo-1c-prompt-analysis` first

### Step 2: Generate Four Digests

**Each digest starts with:**
```markdown
# {TICKER} Q{NUM} {YEAR} — {Level} Digest
**Generated:** {date}
**Source:** {analysis file}
**Signal:** {overall signal}
---
```

**Analyst** (800-1200 words):
- All findings with full quotes and raw data points
- Growth rates, margins, guidance numbers for financial models
- Management tone with specific language examples
- Risk assessment with trend direction
- Action items for model updates and senior review

**Associate** (400-600 words):
- Key themes synthesized across all prompts (not prompt-by-prompt)
- Risk flags with severity ranking
- Prior quarter comparison signals
- 2-3 most important quotes only
- Suggested follow-up questions for management

**VP** (200-300 words):
- Executive summary (3-4 sentences)
- Thesis alignment score + one-line rationale
- Only material changes from prior quarters
- Hold/increase/decrease/exit signal
- One key risk to watch

**Partner** (50-100 words):
- One paragraph verdict
- Binary signal: ATTENTION NEEDED or NO ACTION REQUIRED
- If no material change: "No material change. Thesis intact."

### Step 3: Save
Write each to:
- `digests/{TICKER}_{YEAR}_Q{NUM}_analyst.md`
- `digests/{TICKER}_{YEAR}_Q{NUM}_associate.md`
- `digests/{TICKER}_{YEAR}_Q{NUM}_vp.md`
- `digests/{TICKER}_{YEAR}_Q{NUM}_partner.md`
- Copy all to `{GBLAI_GDRIVE_PATH}/digests/`

### Step 4: Report
Show word count comparison and preview the partner digest.

## Examples

**Example 1:** User says "Create digests for NVDA Q1 2024"
- Reads `analyses/NVDA_2024_Q1_gdoc.md`
- Produces 4 files: analyst (932w), associate (639w), VP (247w), partner (98w)
- Partner digest: "NVIDIA guided Q2 to $11B... Recommend increasing conviction."

## Common Issues

### Word counts off target
The word counts are guidelines. Analyst should always be longest, partner always shortest and genuinely one paragraph.
