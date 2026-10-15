# Business Case

## 1. Problem statement

Regional health authorities must decide where to add hospital capacity under tight budgets. Three structural problems make this hard in Saarland:

1. **Concentrated supply.** In the 2021 baseline, about 40% of beds (3,087 of 7,759) sit in Regionalverband Saarbrücken. Saarpfalz-Kreis has 1,516 beds but only two sites, so many residents face long trips.
2. **Unequal need.** Districts differ in socioeconomic deprivation (GISD), share of residents aged 65+, and projected demand to 2030. Those groups depend on nearby inpatient care the most.
3. **Access gaps.** Average drive time to the nearest hospital ranges from ~12 min (Saarbrücken) to ~19 min (Saarpfalz-Kreis, St. Wendel), and 95th-percentile trips approach 30 minutes in Merzig-Wadern.

Planning by headcount alone (beds proportional to population) does not address these gaps and can widen them.

## 2. Value proposition

| For | Value delivered |
|---|---|
| **State health ministry / planners** | Transparent, repeatable recommendations for new sites and bed counts, with the equity rationale made explicit and auditable |
| **Hospital operators** | Evidence on which catchments are under-served, and early warning on locations already well served nearby (oversaturation penalty) |
| **Residents, especially elderly and deprived populations** | Shorter drive times to inpatient care. The best scenario cuts median access time by 27.5% |
| **Funders / budget holders** | Comparable outcomes from a much smaller bed commitment (200 vs 1,500 beds in the tested scenarios), and a framework to test "what if" budgets |

### Differentiators

- **Equity by design:** a composite Equity Index puts deprivation, ageing, capacity, demand growth, and travel burden into the objective itself, not just into post-hoc reporting.
- **Real-world geography:** travel times come from the OpenStreetMap drive network, not straight-line distance.
- **Policy levers, not black boxes:** weights, bed limits, travel-time thresholds, and spacing rules are explicit parameters a planner can adjust and defend.
- **Self-tuning:** CMA-ES searches the weight space automatically to minimise average travel time, so a good trade-off doesn't depend on manual weight-picking.

## 3. Stakeholders

| Stakeholder | Interest | Role |
|---|---|---|
| Saarland Ministry of Health (Ministerium für Arbeit, Soziales, Frauen und Gesundheit) | Hospital planning (Krankenhausplan), equity of access | Primary user / decision maker |
| District administrations (Landkreise) | Local service levels | Input providers, beneficiaries |
| Hospital operators and associations | Capacity, viability, competition | Consulted on siting constraints |
| Health insurers (Krankenkassen) | Utilisation, cost efficiency | Consulted on demand and cost data |
| Patient and elderly-care advocates | Equitable access | Validate equity weighting |
| Project team (developers / researchers) | Model quality, extensibility | Builders and maintainers |

## 4. Use cases

1. **Capacity expansion planning:** "We can fund *k* new sites and *N* beds. Where should they go?"
2. **Policy scenario comparison:** compare population-proportional, demand-led, deprivation-led, and access-led strategies side by side on the same KPIs.
3. **Equity impact assessment:** quantify how a proposed plan changes travel times and the Equity Index per district before committing funds.
4. **Budget sensitivity:** test how outcomes change as the bed budget, per-site limits, or the 30-minute access threshold change.
5. **Communication:** interactive maps (`src/results/`, `src/experiments/results/maps/`) show existing and proposed sites to non-technical audiences.

## 5. Scope

- **In scope:** six Saarland districts (AGS 10041–10046), 2021 reference year, demand forecasts to 2030, acute inpatient bed planning.
- **Out of scope (current version):** construction and operating costs, staffing, specialty mix (e.g. maternity, stroke units), cross-border (France/Luxembourg/Rhineland-Palatinate) flows, and closures or downsizing of existing hospitals.

## 6. Success criteria

| Criterion | Target / signal |
|---|---|
| Mean drive time to nearest hospital | Lower than the status quo and the population-proportional baseline |
| Median and 95th-percentile drive time | No district worse than status quo |
| Equity | Improvement concentrated in districts with the highest Equity Index |
| Resource use | Comparable or better access per bed added |
| Adoption | Planners can rerun scenarios with new weights/data without code changes (roadmap item) |
