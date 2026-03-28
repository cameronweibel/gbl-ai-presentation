# Simple Analysis JSON Output Schema

Full schema for the structured JSON output. See SKILL.md for the condensed version.

## Field Details

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| ticker | string | uppercase | Stock ticker symbol |
| year | integer | 4 digits | Fiscal year |
| quarter | string | Q1-Q4 | Fiscal quarter |
| analysis_date | string | YYYY-MM-DD | Date analysis was run |
| value_chain_changes | string | 2-3 sentences | Shifts in suppliers, customers, competitive positioning |
| end_market_growth | string | 2-3 sentences per segment | Growth trajectory by business segment |
| management_confidence_score | integer | 1-10 | 1=deeply pessimistic, 10=extremely bullish |
| management_confidence_rationale | string | 1-2 sentences | Evidence for the score |
| capital_allocation_signals | object | 3 fields | M&A stance, capex, buybacks |
| guidance_credibility | string | HIGH/MEDIUM/LOW | Based on specificity and tone |
| guidance_credibility_rationale | string | 1-2 sentences | Evidence for the rating |
| top_3_risks | array | exactly 3 items | Each with risk, severity (H/M/L), trend (INC/STABLE/DEC) |
| notable_quotes | array | 1+ items | Each with speaker, quote, context |
| thesis_alignment_score | integer | 1-10 | Alignment with growth/quality thesis |
| thesis_alignment_rationale | string | 1-2 sentences | Evidence for the score |
