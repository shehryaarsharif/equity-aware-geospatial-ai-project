# Executive Summary

## The problem

Hospital capacity in Saarland is unevenly distributed. In the 2021 baseline used by this project, **28 hospitals with 7,759 beds** serve the state. **12 of the 28 hospitals (3,087 beds, ~40%) are in Regionalverband Saarbrücken**, while rural districts such as St. Wendel and Merzig-Wadern have 2–3 hospitals each. Residents of Saarpfalz-Kreis, St. Wendel, and Merzig-Wadern face average drive times to the nearest hospital of **~18–19 minutes**, compared with **~12 minutes** in Saarbrücken.

Traditional capacity planning allocates beds by population size, and that tends to reinforce existing concentrations. It does not explicitly account for socioeconomic deprivation, ageing populations, future demand growth, or travel burden.

## The solution

The **Equity-Aware Geospatial AI Planner** recommends **where to build new hospitals and how many beds to give each one**, balancing:

- **Equity**: prioritise districts with higher deprivation, more elderly residents, lower existing capacity, higher forecast demand, and longer travel times. These five signals are combined into a single district-level **Equity Index**.
- **Access**: minimise drive time on the real road network (OpenStreetMap) and only count a district as covered if a hospital is reachable within 30 minutes.
- **Efficiency**: respect a total bed budget and min/max beds per site, and avoid building next to existing hospitals or to each other.

A mixed-integer optimisation model (PuLP) chooses the sites and bed counts. An evolutionary search (CMA-ES) then tunes the trade-off weights to minimise average travel time across districts.

## Headline results (10 new sites, compared with the status quo)

| Scenario | Mean drive time | Change | Median drive time | Change |
|---|---|---|---|---|
| Status quo (no new sites) | 16.88 min | – | 16.57 min | – |
| Population-proportional ("policy maker") baseline, 1,500 beds | 15.08 min | −10.7% | 14.33 min | −13.5% |
| Deprivation-aware (full Equity Index), 200 beds | 14.85 min | −12.0% | 12.02 min | **−27.5%** |
| Accessibility-focused (travel time only), 200 beds | **14.71 min** | **−12.9%** | 14.21 min | −14.2% |

**Key takeaway:** the equity- and access-driven scenarios beat the population-proportional baseline on drive time while committing far fewer new beds (200 vs 1,500), because *where* capacity goes matters more than *how much*. The deprivation-aware scenario gives the largest improvement in median access.

## Recommendation

Use the planner as a **scenario-analysis tool** for regional health planning. It is not yet a basis for final siting decisions. Before any operational use, the next steps are to validate demand inputs with real district-level inpatient data, add a cost model, and run sensitivity analysis on the Equity Index weights. See [Risks & Roadmap](risks-and-roadmap.md).
