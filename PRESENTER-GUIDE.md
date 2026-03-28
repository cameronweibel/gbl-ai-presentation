# Demo 1 Presenter Guide: Transcript Analysis Pipeline

## Quick Reference

| ID | Skill Name | Slash Command | Type | What It Does |
|----|-----------|---------------|------|-------------|
| 1A | demo-1a-transcript-downloader | `/demo-1a-transcript-downloader` | Skill (reusable) | Fetches earnings transcript from FMP API, saves to GSheet + GDrive |
| 1B | demo-1b-simple-analysis | `/demo-1b-simple-analysis` | Agent | Hardcoded qualitative screen, outputs structured JSON |
| 1C | demo-1c-prompt-analysis | `/demo-1c-prompt-analysis` | Agent | Custom prompts from YAML file, management-defined questions |
| 1D | demo-1d-digester | `/demo-1d-digester` | Agent | Same analysis tailored for 4 seniority levels |
| 1E | demo-1e-communicator | `/demo-1e-communicator` | Agent | Routes digests to Slack/email at each person's cadence |

## Before the Demo

### Prerequisites
1. `.env` file exists with `FMP_API_KEY` and `GBLAI_GDRIVE_PATH`
2. `prompts/NVDA.yaml` exists with 6 analysis prompts
3. Google Drive is mounted and accessible
4. Claude Code is open in the `gbl-ai-presentation` directory

### Clean Slate (if re-running)
```bash
rm -rf transcripts/*.txt analyses/ digests/ deliveries/
```

## Demo Script

### Act 1: "The Plumbing" (5 min)

**What you say:** "Let's start by connecting to an API. I'm going to use Wispr Flow to dictate what I want."

**What you type:**
```
/demo-1a-transcript-downloader NVDA 2024 Q1
```

**What happens:**
- Calls FMP API, downloads 9,004-word NVDA Q1 2024 earnings transcript
- Saves to `transcripts/NVDA_2024_Q1.txt` + Google Drive
- Creates `transcripts/index.csv` with metadata

**Key talking point:** "This is a *skill*, not an agent. It's dumb plumbing — but it's reusable. Every downstream agent calls this same skill."

---

### Act 2: "Hello World Agent" (8 min)

**What you say:** "Now let's do something with this transcript. The simplest possible analysis."

**What you type:**
```
/demo-1b-simple-analysis NVDA 2024 Q1
```

**What happens:**
- Reads the transcript, produces structured JSON
- Management confidence: 9/10, Guidance credibility: HIGH
- Top 3 risks with severity and trend direction
- Saves to `analyses/NVDA_2024_Q1_simple.json`

**Key talking point:** "This already saves 3-4 hours. But the prompts are hardcoded. What if your partners want different questions for different companies? That's the next agent."

---

### Act 3: "The Curation Layer" (8 min)

**What you say:** "Now I'll show you where the proprietary edge lives."

**Show the audience:** Open `prompts/NVDA.yaml` — show the 6 prompts.

**What you type:**
```
/demo-1c-prompt-analysis NVDA 2024 Q1
```

**What happens:**
- Reads 6 prompts from the YAML file
- Runs each prompt independently against the transcript
- Produces a markdown report with signal/confidence per prompt
- Executive summary table at the end
- Saves to `analyses/NVDA_2024_Q1_gdoc.md`

**Key talking point:** "Anyone on the team adds prompts — no code changes. Different prompts per portfolio company. This is where decades of GBL judgment becomes the AI's instructions. The LLM is commodity. These prompts are the moat."

---

### Act 4: "Four Noise Levels" (5 min)

**What you say:** "Same data, but an analyst needs something very different from a partner."

**What you type:**
```
/demo-1d-digester NVDA 2024 Q1
```

**What happens:**
- Reads the full analysis from Act 3
- Produces 4 digests:
  - **Analyst:** 800-1200 words (full detail, model-ready data)
  - **Associate:** 400-600 words (key themes, risk flags)
  - **VP:** 200-300 words (executive summary, hold/increase signal)
  - **Partner:** 50-100 words (one paragraph verdict)

**Key talking point:** "The partner doesn't want 10 pages. They want 'should I care about this?' That's a 98-word answer."

---

### Act 5: "The Last Mile" (2 min)

**What you say:** "Now we deliver."

**What you type:**
```
/demo-1e-communicator NVDA 2024 Q1
```

**What happens:**
- Wraps each digest with routing metadata
- Analyst → Slack #earnings-analysis (real-time)
- Partner → Email + Slack DM (weekly, critical priority)
- Creates delivery log CSV
- Copies everything to Google Drive

**Key talking point:** "Package this as a scheduled skill. Runs every earnings season automatically. From demo to enterprise workflow in 28 minutes."

---

### Closer

**What you say:** "You are sitting on a goldmine of enterprise data. Without your proprietary process, none of this can be hyper-curated. The LLM is commodity. Your judgment, encoded as prompts and workflows — that's the moat."

## Data Flow

```
/demo-1a  →  transcripts/NVDA_2024_Q1.txt
                    ↓
/demo-1b  →  analyses/NVDA_2024_Q1_simple.json
                    ↓
/demo-1c  →  analyses/NVDA_2024_Q1_gdoc.md
          ←  prompts/NVDA.yaml
                    ↓
/demo-1d  →  digests/NVDA_2024_Q1_{analyst,associate,vp,partner}.md
                    ↓
/demo-1e  →  deliveries/NVDA_2024_Q1_{level}_delivery.md
          →  deliveries/delivery_log.csv
          →  Google Drive (all files)
```

## File Structure

```
gbl-ai-presentation/
├── .env                                    # API keys + GDrive path
├── .claude/skills/
│   ├── demo-1a-transcript-downloader/
│   │   └── SKILL.md
│   ├── demo-1b-simple-analysis/
│   │   ├── SKILL.md
│   │   └── references/output-schema.md
│   ├── demo-1c-prompt-analysis/
│   │   └── SKILL.md
│   ├── demo-1d-digester/
│   │   ├── SKILL.md
│   │   └── references/digest-specs.md
│   └── demo-1e-communicator/
│       └── SKILL.md
├── prompts/
│   └── NVDA.yaml                           # Management-defined prompts
├── transcripts/                            # Downloaded transcripts
├── analyses/                               # Analysis outputs
├── digests/                                # Per-level digests
└── deliveries/                             # Delivery packages + log
```

## Troubleshooting During Demo

| Problem | Fix |
|---------|-----|
| FMP API returns error | Check `.env` has correct `FMP_API_KEY`. Use stable endpoint. |
| "Transcript not found" | Run `/demo-1a-transcript-downloader` first |
| GDrive copy fails | Check Google Drive is mounted. Skill will still save locally. |
| Wrong word counts in digests | Re-run `/demo-1d-digester`. Word counts are guidelines. |
| Slack not posting | Expected — add `SLACK_WEBHOOK_URL` to `.env` or skip for demo |

## Why This Order Matters

1A → 1B shows "zero to useful" in minutes
1B → 1C shows "hardcoded to customizable" (the curation leap)
1C → 1D shows "one-size-fits-all to role-aware" (the hierarchy leap)
1D → 1E shows "analysis to delivery" (the last mile)

Each step builds naturally. Never skip 1A — the audience needs to see data flow from API to output.
