---
name: Assess credit risk with Syntage insights and score
description: Compute and read an entity's Syntage Score plus financial-ratio, risk, and summary insights for a lending decision.
api: openapi/syntage-openapi-original.yml
operations: [CalculateSyntageScore, GetScoresInsight, GetSummaryInsight, GetFinancialRatiosInsight, GetRisksInsight, GetCashFlowInsight, ExportInsight]
---

# Assess credit risk with Syntage insights and score

Once an entity has extracted fiscal data (see the onboard-and-extract skill), compute risk analytics.

## Conventions
- `X-API-Key` header auth; `Accept: application/ld+json`.
- Insights are read as `GET /entities/{entityId}/insights/...`. They require prior extraction to have data.

## Steps
1. **Trigger the score** — `CalculateSyntageScore` (`POST /entities/{entityId}/datasources/syntage/score/calculate`). This starts an async score calculation.
2. **Read the score** — `GetScoresInsight` (`GET /entities/{entityId}/insights/metrics/scores`).
3. **Read the summary** — `GetSummaryInsight` (`GET /entities/{entityId}/insights/summary`) for the headline fiscal profile.
4. **Read financial ratios** — `GetFinancialRatiosInsight` (`GET /entities/{entityId}/insights/financial-ratios`).
5. **Read risk flags** — `GetRisksInsight` (`GET /entities/{entityId}/insights/risks`) and cash flow via `GetCashFlowInsight` (`GET /entities/{entityId}/insights/cash-flow-stats`).
6. **Export for the file** — `ExportInsight` (`GET /entities/{entityId}/insights/{insight}/export`) to produce a downloadable artifact.

## Notes
- Insight collections that are empty usually mean the extraction has not completed or the datasource returned no records — check `ListEntityEvents` before concluding.
