# Solution Overview

## How it works (plain language)

```
 Public data          Five district indexes        Equity Index         Optimiser              Output
 ───────────         ─────────────────────        ────────────         ─────────              ──────
 Destatis, RKI,  ──▶  Demand forecast       ┐
 GISD, hospital       Elderly share         │                         Pick k sites +
 directory, OSM       Deprivation (GISD)    ├──▶  weighted sum  ──▶   beds per site   ──▶   Map + table of
                      Hospital capacity     │     per district        (MILP, PuLP)          new hospitals
                      Travel time           ┘                         tuned by CMA-ES
```

1. **Score each district on need.** Five indexes, each scaled 0–1, capture a different dimension of need.
2. **Combine into an Equity Index.** A weighted sum. Higher means more inequitable, so higher priority.
3. **Generate candidate sites.** Sample points around each district centroid (default 5 km radius), clipped to the Saarland boundary.
4. **Compute drive times.** Shortest drive time on the OpenStreetMap road network from district sample points to candidates and existing hospitals.
5. **Optimise.** Choose exactly *k* sites and a bed count for each to maximise equity-weighted coverage and bed value, minus travel time and oversupply penalties, subject to constraints.
6. **Tune.** CMA-ES searches the objective weights and keeps the set that minimises average district travel time.
7. **Report.** Interactive HTML map and tabular output of existing and predicted hospitals.

## The five need indexes

| Index | What it measures | Source module |
|---|---|---|
| **Demand Forecast** | Projected inpatient demand to 2030 (ARIMA on per-capita demand × population projection, allocated to districts, confidence-weighted) | `src/index_demand_forecast` |
| **Elderly Share** | Share of residents aged 65+ (2021-12-31) | `src/index_elderly_share` |
| **GISD** | German Index of Socioeconomic Deprivation, 2021, min-max scaled | `src/index_gisd` |
| **Hospital Capacity** | Inverted, normalised beds per capita (higher = fewer beds per resident) | `src/index_hospital_capacity` |
| **Travel Time (TAI)** | Scaled mean drive time from 5 sample points per district to the nearest hospital | `src/index_travel_accessibility` |

**Equity Index formula** (`src/main.py`):

```
Accessibility = w5·ElderlyShare + w6·HospitalCapacity
EquityIndex   = w1·DemandForecast + w2·GISD + w3·TravelTime + w4·Accessibility
```

The current default sets all weights to 0.25.

## Optimisation model

**Decision:** which candidate sites to open, which districts each serves, and how many beds each gets.

**Objective (maximise):**

- `+ equity_weight` × equity-index-weighted district coverage
- `− travel_weight` × normalised travel time of assignments
- `+ beds_weight` × beds placed where they cover demand
- `− supply_weight` × existing capacity already nearby (oversaturation penalty)

**Constraints:**

- Open exactly *k* new hospitals
- Districts may only be assigned to open sites reachable within the **time threshold** (default 30 min)
- Beds per new site between **min** (20) and **max** (300), and total new beds ≤ **budget** (1,500)
- Do not open a candidate that has too many existing hospitals within the **existing-hospital distance threshold** (5–7 km)
- New sites must be at least the **candidate-to-candidate distance threshold** (5–7 km) apart

## Policy levers

These are the parameters a planner would adjust. All are plain arguments in `src/main.py` and the experiment notebooks.

| Lever | Default | Business meaning |
|---|---|---|
| `k` | 10 in experiments, 2 in `main.py` | Number of new sites funded |
| `max_beds` | 1,500 | Total new-bed budget |
| `min_beds_per_hospital` / `max_beds_per_hospital` | 20 / 300 | Viable site size range |
| `time_threshold` | 30 min | Access standard used to define coverage |
| `eh_distance_threshold`, `num_neighbors` | 5–7 km, 2 | How close to existing hospitals a new site may be |
| `c2c_distance_threshold` | 5–7 km | Minimum spacing between new sites |
| Equity Index weights `w1…w6` | 0.25 each | Relative importance of demand, deprivation, travel, ageing, capacity |
| Objective weights | equity 0.75, travel/beds/supply 0.065 (CMA-ES start) | Trade-off between equity, access, bed value, oversupply |

## Outputs

- **Interactive maps** (Folium HTML) of existing and predicted hospitals: `src/results/*.html`, `src/experiments/results/maps/*.html`
- **Tables** of sites with coordinates, district, bed allocation, and type (existing/predicted): `src/experiments/results/*.xlsx`
- **Evaluation metrics** per scenario: `src/evaluation/`. See [Results & KPIs](results-and-kpis.md).

## Technology

Python 3.13 · pandas / GeoPandas / Shapely · OSMnx + NetworkX (road network, drive times) · PuLP (MILP) · `cma` (CMA-ES) · statsmodels (ARIMA) · Folium (maps). An alternative prototype planner (`src/ai_planner/agentic_ai_planner_2.py`) explores a cost-vs-equity formulation with Bayesian optimisation.
