# Risks & Roadmap

## Risk register

| # | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R1 | Demand proxy (infectious-disease counts) misallocates demand across districts | High | High | Get district-level inpatient data (e.g. DRG statistics, Krankenkassen data) and replace the proxy |
| R2 | Equity weights seen as arbitrary or politically biased | Medium | High | Run stakeholder workshops to set weights, publish sensitivity analysis, and offer preset "policy profiles" |
| R3 | Recommendations ignore cost and staffing, so they're infeasible | High | High | Add a capex/opex model and workforce constraints, and treat outputs as scenarios rather than decisions |
| R4 | Results inconsistent or not reproducible (stochastic search, legacy files, evaluation anomalies) | Medium | Medium | Fix random seeds, regenerate all results in one pipeline, add regression tests on KPIs |
| R5 | Stale data (2021 baseline) | Medium | Medium | Parameterise reference year and set up an annual data refresh |
| R6 | Car-only access underestimates burden for vulnerable groups | Medium | Medium | Add public transport travel times (GTFS) and ambulance response times |
| R7 | Data licensing / attribution breaches on publication | Low | Medium | Licence review for OSM (ODbL), GISD, and Destatis, with attribution in maps and reports |
| R8 | Over-reliance on a model for a high-stakes public decision | Medium | High | Keep a human in the loop, document assumptions, and present trade-offs across scenarios |

## Open questions

1. Who is the target decision maker: the state ministry, a hospital group, or an academic audience? This drives scope (cost model, specialties).
2. Should the demand-forecast index be inverted? Confirm direction (see [Data & Assumptions](data-and-assumptions.md)).
3. What access standard applies: 30 min by car, or a different threshold per care level?
4. Should consolidation and closure of existing sites be in scope?
5. Should cross-border patient flows be modelled?

## Roadmap

### Phase 1: Validate (0–2 months)
- Regenerate all scenario results in a single reproducible pipeline with fixed seeds
- Persist the full KPI set (HDR, HFDR, Equity Score, Gini) alongside drive times
- Investigate counter-intuitive district results (e.g. St. Wendel in the main scenario)
- Confirm index directions and weight definitions with domain experts
- Run sensitivity analysis on Equity Index weights and the 30-minute threshold

### Phase 2: Strengthen the model (2–5 months)
- Replace the disease-count proxy with real district-level inpatient data
- Move to population-weighted sample points or municipality-level resolution
- Add a cost model (capex per bed, opex) and an explicit budget constraint
- Add public transport and ambulance accessibility
- Extend candidate generation beyond centroid radius (e.g. land-use-aware sites)

### Phase 3: Productise (5–9 months)
- Planner-facing web interface: adjust levers, run scenarios, compare KPIs and maps side by side
- Scenario library and exportable reports for committee papers
- Configurable region, so the tool generalises beyond Saarland to other German states
- Packaging, CI, and automated data refresh

### Phase 4: Pilot (9–12 months)
- Pilot with a regional health authority on a live planning question
- Collect feedback on usability, trust, and decision impact
- Publish methodology and results

## Measures of success for the next phase

- Reproducible results: the same inputs produce the same KPIs
- Full KPI table published for all scenarios
- Stakeholder-agreed weight profile documented
- At least one external expert review of methodology
