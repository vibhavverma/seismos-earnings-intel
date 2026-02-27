# Earnings Intelligence Data Schema

## File: `earnings-intel-Q4-2025.json`

Array of company objects. Each record follows this schema exactly — all fields present on every record. Empty values are `""` for strings, `[]` for arrays, `{}` for dicts.

### Identity Fields
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `ticker` | string | yes | Trading symbol (e.g., `FANG`, `TOU.TO`, `2222.SR`) |
| `company` | string | yes | Full company name |
| `segment_raw` | string | yes | Original segment classification from research |
| `segment_normalized` | string | yes | One of: `Operator`, `OFS-Pumper`, `OFS-Other`, `Major`, `International` |
| `exchange` | string | yes | `NYSE/NASDAQ`, `TSX`, `ASX`, `Tadawul`, `SSE` |
| `country` | string | yes | HQ country (US, Canada, Argentina, etc.) |

### Temporal Fields
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `quarter` | string | yes | Reporting period (e.g., `Q4 2025`) |
| `last_updated` | string | yes | ISO date of last data update (e.g., `2026-02-27`) |

### Earnings Calendar
| Field | Type | Description |
|-------|------|-------------|
| `earnings_calendar.last_reported` | string/null | Last quarter reported (e.g., `Q4 2025`) |
| `earnings_calendar.last_report_date` | string/null | ISO date of last report |
| `earnings_calendar.next_earnings_date` | string/null | ISO date of next expected report |
| `earnings_calendar.next_earnings_status` | string/null | `expected` (confirmed), `estimated` (inferred), `acquired` (no more reports) |
| `earnings_calendar.fiscal_quarter` | string/null | Quarter being reported next |
| `earnings_calendar.note` | string | Optional context (e.g., fiscal year info) |

### Intelligence Fields (free text)
| Field | Type | Fill Rate | Description |
|-------|------|-----------|-------------|
| `strategy_summary` | string | 100% | 2-3 sentence strategy overview from earnings call |
| `capex_guidance` | string | 100% | Capital expenditure guidance with dollar amounts |
| `production_guidance` | string | 57% | Production targets (BOE/d, Bcf/d, etc.) |
| `efficiency_metrics` | string | 57% | Completion efficiency data ($/ft, stages/day, etc.) |
| `completions_strategy` | string | 100% | How company approaches completions (frac design, tech adoption) |
| `notable_quote` | string | 100% | Key quote from earnings call |

### Structured Arrays
| Field | Type | Fill Rate | Description |
|-------|------|-----------|-------------|
| `basin_focus` | string[] | 100% | Operating basins (e.g., `["Permian Basin", "Eagle Ford"]`) |
| `technology_mentions` | string[] | 92% | Technologies discussed (e.g., `["Zeus IQ", "e-frac"]`) |
| `service_provider_mentions` | string[] | 59% | Named service providers (e.g., `["Halliburton", "Seismos"]`) |

### Structured Metrics
| Field | Type | Fill Rate | Description |
|-------|------|-----------|-------------|
| `metrics_reported` | object | 26% | Key-value pairs of reported financials (e.g., `{"Q4_revenue": "$403M"}`) |

### Seismos Scoring
| Field | Type | Fill Rate | Description |
|-------|------|-----------|-------------|
| `seismos_relevance` | string | 100% | `High`, `Medium`, or `Low` |
| `seismos_relevance_reason` | string | 100% | 1-2 sentence explanation of scoring |
| `seismos_relevance_detail` | string | 43% | Extended analysis (High-relevance companies only) |

### Data Provenance
| Field | Type | Fill Rate | Description |
|-------|------|-----------|-------------|
| `data_quality` | string | 59% | `Full transcript`, `Search summary`, `Limited data` |
| `transcript_url` | string | 43% | Source of transcript data |

## Segment Normalization Rules

```
Pumper / Completion Services → OFS-Pumper
Operator / E&P              → Operator
OFS-* / Chemical / Drilling → OFS-Other
Major / Supermajor          → Major
International / Canada      → International
```

## Future Ingestion

To add a new quarter:
1. Run the earnings intelligence agent with updated watchlist
2. Ensure output matches this schema (all fields present)
3. Update `quarter` and `last_updated` fields
4. Update `earnings_calendar` with new dates
5. Regenerate CSV with `python3 scripts/regenerate_csv.py`
