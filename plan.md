# Demo 1 Build Plan: Reported Transcript Analysis Pipeline

## Overview

Build and test 5 skills/agents end-to-end in Claude Code. Each lives in `.claude/skills/` following project conventions. We build them in order — each step depends on the previous.

**Target example:** NVDA (NVIDIA) Q1 2024 earnings transcript.

## Decisions Made

- **Transcript API:** FMP (financialmodelingprep.com) stable endpoint. API key in `.env`. Tested and working.
- **Storage:** Mounted Google Drive at `GBLAI_GDRIVE_PATH` + local files. Excel/GSheet-compatible outputs.
- **Slack:** Real webhook if we can set it up in browser, otherwise simulated locally via delivery files.

---

## Step 1: `/transcript-downloader` (Reusable Skill) — BUILT

**Location:** `.claude/skills/transcript-downloader/SKILL.md`
**Status:** SKILL.md written, ready to test

**What it does:**
- Takes ticker + year + quarter as input (e.g., `NVDA 2024 Q1`)
- Calls FMP stable API: `https://financialmodelingprep.com/stable/earning-call-transcript`
- Saves raw transcript to `transcripts/` (local) and GDrive
- Logs metadata to `transcripts/index.csv`

**Test:** `/transcript-downloader NVDA 2024 Q1`
- [ ] Transcript saved to `transcripts/NVDA_2024_Q1.txt`
- [ ] Transcript copied to GDrive `transcripts/` folder
- [ ] `index.csv` created/updated with metadata row
- [ ] Word count and preview reported to user

---

## Step 2: `/simple-transcript-analysis` (Single Agent) — BUILT

**Location:** `.claude/skills/simple-transcript-analysis/SKILL.md`
**Status:** SKILL.md written, ready to test

**What it does:**
- Reads transcript from Step 1
- Runs hardcoded qualitative screen (value chain, growth, confidence score 1-10, capital allocation, guidance credibility, top 3 risks, notable quotes, thesis alignment)
- Outputs structured JSON to `analyses/`

**Test:** `/simple-transcript-analysis NVDA 2024 Q1`
- [ ] JSON saved to `analyses/NVDA_2024_Q1_simple.json`
- [ ] All fields populated with reasonable NVDA-specific content
- [ ] Management confidence score is a number 1-10
- [ ] Guidance credibility is HIGH/MEDIUM/LOW
- [ ] Top 3 risks have severity + trend

---

## Step 3: `/shared-gdoc-prompt-analysis` (Human-Augmented Agent) — BUILT

**Location:** `.claude/skills/shared-gdoc-prompt-analysis/SKILL.md`
**Status:** SKILL.md written, `prompts/NVDA.yaml` created with 6 prompts

**What it does:**
- Reads custom prompts from `prompts/{TICKER}.yaml`
- Runs each prompt independently against the transcript
- Produces a structured markdown report with finding, quotes, signal, confidence per prompt
- Includes executive summary table

**Test:** `/shared-gdoc-prompt-analysis NVDA 2024 Q1`
- [ ] Reads all 6 prompts from `prompts/NVDA.yaml`
- [ ] Markdown report saved to `analyses/NVDA_2024_Q1_gdoc.md`
- [ ] Each of the 6 prompts has its own section with finding + signal
- [ ] Executive summary table at the end
- [ ] Report copied to GDrive

---

## Step 4: `/ai-digester` (Hierarchy-Aware Agent) — BUILT

**Location:** `.claude/skills/ai-digester/SKILL.md`
**Status:** SKILL.md written, ready to test

**What it does:**
- Reads full analysis from Step 3 (gdoc.md) or Step 2 (simple.json)
- Produces 4 tailored digests:
  - **Analyst** (800-1200 words): full detail, all quotes, model-ready data
  - **Associate** (400-600 words): key themes, risk flags, follow-up questions
  - **VP** (200-300 words): executive summary, thesis alignment, hold/increase/decrease signal
  - **Partner** (50-100 words): one-paragraph verdict, ATTENTION NEEDED or NO ACTION REQUIRED

**Test:** `/ai-digester NVDA 2024 Q1`
- [ ] 4 files saved to `digests/NVDA_2024_Q1_{level}.md`
- [ ] Analyst digest is longest, partner is shortest
- [ ] Partner digest is genuinely one paragraph
- [ ] Each digest has correct header with metadata
- [ ] Digests copied to GDrive

---

## Step 5: `/communicator` (Delivery Agent) — BUILT

**Location:** `.claude/skills/communicator/SKILL.md`
**Status:** SKILL.md written, ready to test

**What it does:**
- Reads all 4 digests from Step 4
- Creates delivery packages with routing metadata (channel, frequency, priority)
- Attempts Slack delivery if webhook configured in `.env`
- Logs all deliveries to `deliveries/delivery_log.csv`

**Test:** `/communicator NVDA 2024 Q1`
- [ ] 4 delivery files in `deliveries/NVDA_2024_Q1_{level}_delivery.md`
- [ ] Each has routing metadata (To, Channel, Frequency, Status)
- [ ] `delivery_log.csv` created/updated
- [ ] Deliveries copied to GDrive
- [ ] Slack webhook attempted if configured (or simulated)

---

## Step 6: End-to-End Pipeline Test

Run the full pipeline in sequence:
```
/transcript-downloader NVDA 2024 Q1
/simple-transcript-analysis NVDA 2024 Q1
/shared-gdoc-prompt-analysis NVDA 2024 Q1
/ai-digester NVDA 2024 Q1
/communicator NVDA 2024 Q1
```

- [ ] All 5 skills run without errors
- [ ] Final output: 4 delivered digests, all saved locally + GDrive
- [ ] `transcripts/index.csv` has the download record
- [ ] `deliveries/delivery_log.csv` has 4 delivery records

---

## File Structure (Current)

```
gbl-ai-presentation/
├── .env                          # FMP_API_KEY + GBLAI_GDRIVE_PATH
├── .gitignore                    # Excludes .env and output dirs
├── .claude/
│   └── skills/
│       ├── transcript-downloader/SKILL.md      ✅
│       ├── simple-transcript-analysis/SKILL.md ✅
│       ├── shared-gdoc-prompt-analysis/SKILL.md ✅
│       ├── ai-digester/SKILL.md                ✅
│       └── communicator/SKILL.md               ✅
├── prompts/
│   └── NVDA.yaml                 ✅ 6 prompts configured
├── transcripts/                  (output — gitignored)
├── analyses/                     (output — gitignored)
├── digests/                      (output — gitignored)
└── deliveries/                   (output — gitignored)
```

---

## Next: Test Each Skill

Ready to test. Run each skill in order, verify outputs, fix issues before moving to the next.
