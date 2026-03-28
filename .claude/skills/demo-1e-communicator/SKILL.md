---
name: demo-1e-communicator
description: Delivers transcript analysis digests to the team via Slack, email simulation, and Google Drive. Routes each seniority level to the right channel at the right frequency. Use when user says "send digests", "deliver to team", "communicate results", "send to slack", or "distribute analysis". Requires digests from demo-1d-digester. This skill does NOT analyze — it only routes and delivers.
---

# Communicator

**Demo 1E** — The last mile. Right info, right person, right cadence.

## Instructions

### Step 1: Load Digests
Read from `digests/`:
- `{TICKER}_{YEAR}_Q{NUM}_analyst.md`
- `{TICKER}_{YEAR}_Q{NUM}_associate.md`
- `{TICKER}_{YEAR}_Q{NUM}_vp.md`
- `{TICKER}_{YEAR}_Q{NUM}_partner.md`

If any are missing, warn and proceed with what's available.

### Step 2: Create Delivery Packages
For each digest, create `deliveries/{TICKER}_{YEAR}_Q{NUM}_{level}_delivery.md`:

```markdown
# Delivery: {TICKER} Q{NUM} {YEAR}
**To:** {role} team
**Channel:** {channel}
**Frequency:** {frequency}
**Sent at:** {ISO timestamp}
**Status:** DELIVERED
---
{digest content}
```

### Routing Table

| Level | Channel | Frequency | Priority |
|-------|---------|-----------|----------|
| Analyst | Slack #earnings-analysis | Real-time | Normal |
| Associate | Slack #investment-team | Daily | Normal |
| VP | Email + Slack DM | Weekly | High (only if material) |
| Partner | Email + Slack DM | Weekly | Critical (only if action needed) |

### Step 3: Slack Delivery
If `SLACK_WEBHOOK_URL` is in `.env`:
```bash
curl -X POST -H 'Content-Type: application/json' -d '{"text":"*{TICKER} Q{NUM} {YEAR} Earnings Alert*\n\n{partner_digest}"}' {WEBHOOK_URL}
```
If no webhook configured, note "Slack: simulated (no webhook)" — this is expected for the demo.

### Step 4: Save Logs
1. Append 4 rows to `deliveries/delivery_log.csv` (header: `ticker,year,quarter,level,channel,timestamp,status,file`)
2. Copy all delivery files to `{GBLAI_GDRIVE_PATH}/deliveries/`

### Step 5: Report
Show delivery summary: which digests delivered, to which channels, file paths, any warnings.

## Examples

**Example 1:** User says "Send the NVDA digests to the team"
- Reads 4 digest files
- Creates 4 delivery packages with routing metadata
- Logs 4 rows to delivery_log.csv
- Reports: "4 digests delivered — analyst to Slack, partner to email"

## Common Issues

### Missing digests
Run `/demo-1d-digester` first. The communicator does not generate analysis.

### No Slack webhook
This is fine for the demo. Deliveries are simulated via local files. Add `SLACK_WEBHOOK_URL` to `.env` to enable real Slack posting.
