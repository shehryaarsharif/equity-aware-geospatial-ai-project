# Data & Assumptions

## Data sources

| Dataset | Used for | Location in repo | Publisher |
|---|---|---|---|
| Hospital directory 2021 (Krankenhausverzeichnis) | Existing hospitals, bed counts, addresses | `src/*/data/**/Krankenhausverzeichnis_2021.xlsx` | Destatis |
| Hospital inpatient counts (annual) | Per-capita demand, current demand | `src/index_demand_forecast/data/hospital_inpatients.csv` | Destatis |
| Population history and projections (variants 01–05) | Per-capita demand, 2030 projection | `src/index_demand_forecast/data/population_*.csv` | Destatis |
| Notifiable disease counts by district (IfSG) | Proxy to split state demand across districts | `src/index_demand_forecast/data/diseases_history.csv` | RKI (SurvStat) |
| Population by age group | Elderly share (65+) | `src/index_elderly_share/data/raw/pop_age_groups.csv` | Regional statistics |
| District population | Beds per capita | `src/index_hospital_capacity/data/District-Population.xlsx` | Statistical office |
| GISD (district level) | Socioeconomic deprivation | `src/index_gisd/data/raw/GISD_Bund_Kreis.csv` | RKI (German Index of Socioeconomic Deprivation) |
| NUTS 2021 level 3 boundaries | District geometries | `src/*/data/raw/NUTS_RG_01M_2021_4326_LEVL_3.shp` | Eurostat / GISCO |
| OpenStreetMap drive network | Drive-time routing | `src/saarland_graph.graphml` (cached via OSMnx) | OpenStreetMap contributors |
| Geocoding | Hospital coordinates from addresses | Nominatim (geopy) | OpenStreetMap |

All sources are public. Check licence and attribution requirements before any external publication, especially for OpenStreetMap (ODbL) and GISD.

## Key assumptions

| # | Assumption | Impact if wrong |
|---|---|---|
| A1 | **Reference year is 2021** for hospitals, GISD, and elderly share | Plans may not reflect closures/openings since 2021 |
| A2 | **State-level inpatient demand can be split across districts using notifiable infectious-disease counts** | Demand per district may be misallocated. Infectious disease is a weak proxy for overall inpatient load |
| A3 | **ARIMA trends to 2030** on per-capita demand and disease counts, with population projection variant 01 | Shocks (e.g. pandemics, policy changes) are not captured |
| A4 | **Car travel on the OSM road network** at modelled edge speeds represents access | Ignores public transport, congestion, ambulance routing, and patients without cars |
| A5 | **Five sample points per district** represent where residents live | Coarse. Population-weighted points would be more accurate |
| A6 | **Equity Index weights are equal (0.25)** | Weighting is a value judgement and should be agreed with stakeholders |
| A7 | **Candidates lie within 5 km of district centroids** | Better sites further from centroids (e.g. in Saarpfalz-Kreis) may be missed |
| A8 | **Beds are interchangeable** (no specialty mix, staffing, or cost per site) | Recommendations may be infeasible operationally or financially |
| A9 | **Saarland is a closed system** | Ignores patients using hospitals in Rhineland-Palatinate, France, or Luxembourg |
| A10 | Existing hospitals stay unchanged | Consolidation or closure scenarios aren't modelled |

## Known limitations

- **Granularity:** only 6 districts. Municipality-level (Gemeinde) analysis would give much finer targeting. `src/experiments/data/SL_Gemeindezahlen_2021.pdf` suggests this was considered.
- **Index direction:** the demand-forecast index is computed as `1 − share` (`forecast_demand_per_district_in_saarland` in `src/index_demand_forecast/demand_forecast.py`), so districts with higher forecast demand get a *lower* score, while the Equity Index treats higher values as worse equity. Confirm the intended direction so that high-demand districts are prioritised, not deprioritised.
- **No cost model in the main planner:** the prototype `agentic_ai_planner_2.py` uses a placeholder `COST_PER_BED = 1500` EUR, which is far below real hospital capex/opex and should not be used for budgeting.
- **Reproducibility:** CMA-ES and the policy-maker baseline are stochastic and seeds aren't fixed. Some result files (`main.xlsx`, `deprivation_aware_model.xlsx` in `src/experiments/results/`) are in a legacy Excel format that standard readers can't open. Copies in `src/evaluation/data/processed/` are readable.
- **Run-time dependencies:** first runs download OSM data and geocode addresses, which needs internet access and is subject to rate limits.
