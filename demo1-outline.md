# Demo 1: Reported Transcript Analysis — Full Outline

## Narrative Arc
**Core message:** You are sitting on a goldmine of enterprise data. Without your proprietary process, none of this can be hyper-curated. The LLM is commodity. Your judgment, encoded as prompts and workflows, is the moat.

---

## Demo Flow (5 Agents/Skills, Built Live)

### Step 1: `/transcript-downloader`
**What:** Skill (not agent) — downloads earnings transcripts from API
**Why a skill:** Multiple agents reuse it in parallel (transcript analysis, portfolio watchdog, digester all need transcripts)
**Live action:**
- **Use Wispr Flow** to dictate instructions to Claude Code (voice-to-code — no typing)
- Say: "Connect to the transcript API and download NVDA Q1 2024"
- Claude Code builds the fetcher, calls the API, returns the raw transcript
- Store in GSheet (GSheet = database)

**Talking points:**
- **Wispr Flow is the interface layer** — you speak naturally, Claude Code translates to code
- This is how non-engineers interact with AI agents: voice → code → execution
- Skills are composable building blocks; agents are the orchestrators
- GSheet as DB means non-technical team members can see/audit everything

---

### Step 2: `/simple-transcript-analysis`
**What:** One agent, simple qualitative screen
**Why:** Show the minimum viable agent — one transcript, one set of prompts, one output
**Live action:**
- Feed NVDA Q1 2024 transcript to Claude API
- Run qualitative screen: **"value chain changes, current and end market growth"**
- Output: structured JSON with scores, signals, key quotes
- Write results back to GSheet

**Talking points:**
- This is the "Hello World" of investment agents
- Already saves 3-4 hours of analyst time per transcript
- But the prompts are hardcoded — what if management wants different questions?

---

### Step 3: `/shared-gdoc-portfolio-prompt-transcript-analysis`
**What:** Human-augmented prompt system — management defines prompts per company in a shared GDrive doc
**Why:** The curation layer. Non-technical users shape what the agent looks for.
**Live action:**
- Show shared GDrive doc with prompts of interest (defined by management)
- Prompts vary per company (SGS has different questions than Adidas)
- Agent reads prompts on every run — no code changes needed
- If new transcript drops → qualitative screen runs automatically
- Output goes to shared GDrive document

**Talking points:**
- **This is where proprietary process becomes proprietary AI** — decades of GBL judgment encoded as prompts
- Management doesn't need to code — they write questions in plain English
- Different prompts per portfolio company = hyper-curated analysis
- The agent is the vehicle; the prompts are the intellectual property

**Example prompts (from GDrive doc):**
```
prompt_1: "Value chain changes in the last quarter"
prompt_2: "Current and end market growth trajectory"
prompt_3: "Management tone on capital allocation"
prompt_4: "Guidance credibility vs prior quarters"
prompt_5: "Key risks and their change in emphasis"
```

---

### Step 4: `/ai-digester`
**What:** Digests output from prompt-transcript-analysis agents, tailored per hierarchy level
**Why:** An analyst needs different depth than a partner. One agent's output, multiple consumption layers.
**Live action:**
- Show how the same transcript analysis gets digested differently:
  - **Analyst:** Full detail, all quotes, model-ready data points
  - **Associate:** Key themes, risk flags, comparison to prior quarter
  - **VP:** Executive summary, thesis alignment score, action items
  - **Partner:** One-paragraph verdict, only flag if something changed materially

**Talking points:**
- Agents that are relevant per rung in the hierarchy
- Same underlying data, different signal-to-noise ratio
- Partners don't want 10 pages — they want "should I care about this?"
- Frequency can also vary: analyst gets real-time, partner gets weekly digest

---

### Step 5: `/communicator`
**What:** Agent that sends aggregated and summarized output to Slack, email, etc.
**Why:** The last mile — getting the right info to the right person at the right time
**Live action:**
- Package the digested output
- Send to Slack channel (or email)
- Show frequency configuration: daily for analysts, weekly for partners
- Demonstrate that each person gets their tailored view

**Talking points:**
- "What frequency? What is interesting for her?" — these are configurable per person
- Can do it by staff level automatically
- The communicator doesn't analyze — it routes and delivers
- This is where the agent system becomes a workflow, not a tool

---

## Then: Package as Scheduled Skill

**Live action:**
- Take the full pipeline and package it as a scheduled skill
- Show it running on a cron: every time a new transcript is filed, the pipeline fires
- End-to-end: transcript drops → download → analyze → digest per level → deliver to Slack

**Talking points:**
- From "I ran this once in a demo" to "this runs every earnings season automatically"
- The skill is the unit of automation; the schedule is the trigger
- This is the bridge from "cool demo" to "enterprise workflow"

---

## Key Themes to Reinforce Throughout Demo 1

### 1. Enterprise Data is the Goldmine
- Without GBL's proprietary process, you can't make this hyper-curated
- The LLM (Claude) is commodity — anyone can call it
- The prompts, the portfolio-specific questions, the hierarchy-aware digesting — that's the moat

### 2. Adoption Strategy (foreshadow slide 14)
- Mandated AI usage does not encourage adoption
- Instead: **Curated Performance Discovery Period**
  - Let people try tools freely
  - Collect what they built
  - Come back in a few weeks
  - Show management how the team actually used it
  - "Here are the approvals needed" — then let them run

### 3. Privacy & Compliance (foreshadow slide 15)
- "Can we use this and be compliant?" — the first question every enterprise asks
- Recommended tech stack: Claude API (zero data retention) + MCP (on-premise connectors)
- Microsoft ecosystem equivalent: Azure OpenAI + Copilot Studio + Power Automate
- MCP equivalent in Microsoft world: Copilot connectors / Graph API

### 4. Using Historical Data for Agent Discovery
- Use their existing workflows and data to discover where agents add value
- Don't start from "what can AI do?" — start from "what do you already do that's repetitive?"
- The discovery process itself surfaces the highest-ROI automation targets

---

## Diagram: Demo 1 Agent Pipeline

```
                    ┌─────────────────────────────────┐
                    │     Shared GDrive Document       │
                    │  (Management-defined prompts     │
                    │   per portfolio company)         │
                    └──────────────┬──────────────────┘
                                   │ reads prompts
                                   ▼
┌──────────┐    ┌──────────────────────────────┐    ┌──────────────┐
│ Transcript│    │  /shared-gdoc-portfolio-     │    │              │
│   API     │───▶│   prompt-transcript-analysis │───▶│ /ai-digester │
│ (Wispr)   │    │                              │    │              │
└──────────┘    └──────────────────────────────┘    └──────┬───────┘
      │                                                     │
      │  ┌──────────────────┐                    ┌──────────┴──────────┐
      └─▶│ /transcript-     │                    │                     │
         │  downloader      │              ┌─────┴─────┐  ┌────┴────┐
         │ (reusable skill) │              │  Analyst   │  │ Partner │
         └──────────────────┘              │  (daily)   │  │(weekly) │
                │                          └─────┬─────┘  └────┬────┘
                ▼                                │             │
         ┌──────────┐                     ┌──────┴─────────────┴──────┐
         │  GSheet  │                     │      /communicator        │
         │   (DB)   │                     │   (Slack / Email / etc)   │
         └──────────┘                     └───────────────────────────┘
```

---

## Slide Mapping

| Slide | Current Content | Update Needed |
|-------|----------------|---------------|
| 5 (Overview) | 3 agent cards | Minor: add "/transcript-downloader" mention |
| 6 (Demo 1 Hands On) | Flow diagram: Fetcher → Analyser → Loop → Anomalies → Briefing | **Major update:** Replace with 5-agent pipeline diagram |
| 7 (Demo 1 Extending) | Shared GDrive prompts code block | Keep as-is, this maps to Step 3 |
| NEW slide? | — | Consider adding a slide showing the per-level digester output |
| 12 (Packaging) | Skill pipeline flow | Already shows the right pipeline, may need minor alignment |

---

## Demo Script (What Cameron Actually Does Live)

1. **[2 min]** Open Claude Code. "We're going to connect to a transcript API."
2. **[5 min]** Build `/transcript-downloader` — call Wispr Flow, get NVDA Q1 2024, show raw text, write to GSheet
3. **[8 min]** Build `/simple-transcript-analysis` — one agent, hardcoded prompts, run against transcript, show JSON output in GSheet
4. **[8 min]** Show shared GDrive doc → build `/shared-gdoc-portfolio-prompt-transcript-analysis` — reads prompts from GDrive, runs per company, auto-triggers on new transcripts
5. **[3 min]** Show `/ai-digester` concept — same output, 4 different views (analyst/associate/VP/partner)
6. **[2 min]** Show `/communicator` — Slack delivery, frequency config
7. **[Bonus]** Package as scheduled skill, show cron config
