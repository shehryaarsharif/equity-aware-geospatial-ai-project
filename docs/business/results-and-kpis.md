# Results & KPIs

## KPI definitions

| KPI | Definition | Direction | Implemented in |
|---|---|---|---|
| **Mean / median / P95 drive time** | Drive time (minutes) from 5 sample points per district to the nearest hospital, aggregated per district and statewide | Lower is better | `src/evaluation/metrics/accessibility_score_metric.py` |
| **Equity Score** | Equity Index recomputed with the scenario's hospital set (capacity and travel components updated) | Lower is better | `src/evaluation/metrics/equity_index_metric.py` |
| **HDR (Hospital-bed-to-Demand Ratio)** | Total beds ÷ inpatients per district (2021) | Closer to balanced across districts is better | `src/evaluation/metrics/hdr_metric.py` |
| **HFDR (Hospital-bed-to-Forecast-Demand Ratio)** | Total beds ÷ forecast inpatient demand per district | Closer to balanced is better | `src/evaluation/metrics/hfdr_metric.py` |
| **Over-served area count** | Districts with HDR > mean + 1.5·SD | Lower is better | `src/evaluation/metrics/overserved_area_metric.py` |
| **Demand-weighted Gini** | Inequality of capacity-to-demand ratio across districts (Lorenz curve) | Lower is better | `src/evaluation/evaluation.py` |

## Scenarios evaluated

All scenarios add **10 new sites** to the 2021 baseline of **28 hospitals / 7,759 beds**, using a 30-minute access threshold and 20–300 beds per site.

| Scenario | Logic | New beds added |
|---|---|---|
| **Status quo** | Existing hospitals only | 0 |
| **Policy maker** | Sites allocated to the 4 most populous districts in proportion to population, placed at random within each district, 1,500 beds split by population | 1,500 |
| **Main (equity-aware)** | Full Equity Index, balanced objective (equity 0.4, travel 0.4, beds 0.3, supply 1.0) | 1,500 |
| **Demand-based** | Equity term uses only the Demand Forecast index | 200 |
| **Deprivation-aware** | Equity term uses the full composite Equity Index, equity-only objective | 200 |
| **Accessibility-based** | Travel-time-only objective | 200 |

> The demand, deprivation, and accessibility scenarios set `beds_weight = 0`, so the optimiser gives each site the minimum of 20 beds (10 × 20 = 200).

## Statewide accessibility results

Source: `src/evaluation/data/processed/accessibility_score.xlsx`

| Scenario | Mean (min) | Δ vs status quo | Median (min) | Δ | P95 (min) | Δ |
|---|---|---|---|---|---|---|
| Status quo | 16.88 | – | 16.57 | – | 27.12 | – |
| Policy maker | 15.08 | −10.7% | 14.33 | −13.5% | 27.12 | 0.0% |
| Main (equity-aware) | 15.86 | −6.0% | 15.36 | −7.3% | 27.98 | +3.2% |
| Demand-based | 15.40 | −8.8% | 12.80 | −22.8% | 27.30 | +0.7% |
| Deprivation-aware | 14.85 | −12.0% | **12.02** | **−27.5%** | 27.12 | 0.0% |
| Accessibility-based | **14.71** | **−12.9%** | 14.21 | −14.2% | **26.17** | **−3.5%** |

## District-level mean drive time (minutes)

Source: `src/evaluation/data/processed/RES_*_model.xlsx`

| District | Status quo | Policy maker | Main | Demand | Deprivation | Accessibility |
|---|---|---|---|---|---|---|
| Merzig-Wadern | 17.81 | 17.47 | 17.47 | 17.47 | 17.47 | **12.37** |
| Neunkirchen | 16.06 | **8.71** | 11.11 | 15.06 | 9.69 | 14.74 |
| Regionalverband Saarbrücken | 12.41 | 12.08 | 12.41 | 12.51 | 12.41 | 12.41 |
| Saarlouis | 17.07 | **14.08** | 15.35 | 17.07 | 16.41 | 17.07 |
| Saarpfalz-Kreis | 18.96 | 19.22 | 18.96 | 18.96 | 18.96 | 19.22 |
| St. Wendel | 18.93 | 18.93 | 19.85 | **11.32** | 14.17 | 12.43 |

## Where new capacity goes

| Scenario | Saarbrücken | Merzig-Wadern | Neunkirchen | Saarlouis | Saarpfalz | St. Wendel |
|---|---|---|---|---|---|---|
| Policy maker | 4 / 638 | – | 2 / 242 | 2 / 367 | 2 / 253 | – |
| Main | 1 / 300 | – | 4 / 540 | 3 / 620 | – | 2 / 40 |
| Demand-based | 2 / 40 | – | 3 / 60 | – | – | 5 / 100 |
| Deprivation-aware | – | – | 5 / 100 | 2 / 40 | – | 3 / 60 |
| Accessibility-based | 2 / 40 | 4 / 80 | 1 / 20 | – | 2 / 40 | 1 / 20 |

*Cells show new sites / new beds.*

## Interpretation

1. **Location beats volume.** Three scenarios that add only 200 beds beat the 1,500-bed population-proportional baseline on mean and median drive time.
2. **Equity-led siting helps the left-behind districts.** The deprivation-aware and demand-based scenarios move capacity to Neunkirchen and St. Wendel and cut median statewide drive time by 23–28%.
3. **Access-led siting cuts the worst trips.** Only the accessibility-based scenario reduces P95 drive time, mainly by adding sites in Merzig-Wadern.
4. **Saarpfalz-Kreis remains the hardest-to-serve district** (~19 min) in every scenario. This is a candidate for targeted intervention or a larger candidate-search radius.
5. **No single scenario wins every KPI.** That supports offering the tool as a scenario-comparison instrument with explicit trade-offs, not a single "optimal" answer.

## Caveats

- Only accessibility results are stored as committed artifacts. HDR, HFDR, Equity Score, and Gini are implemented but their outputs are not saved in the repo. They should be regenerated and added here.
- Some district results are counter-intuitive. In the main scenario, St. Wendel's mean time rises from 18.93 to 19.85 min even though two sites are added there. This points to an evaluation detail (nearest-hospital selection or how sample points are paired with hospitals) that should be reviewed before results are published.
- The policy-maker baseline places sites at random within districts, so its results vary between runs.
