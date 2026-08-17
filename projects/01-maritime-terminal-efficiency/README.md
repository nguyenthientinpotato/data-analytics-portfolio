# International Maritime Logistics & Terminal Efficiency Analysis

The Power BI dashboard analyzes 15,000 cargo movement events at 50 global terminals involving 1,000 vessels (2021 to 2024). It is designed to detect bottlenecks, assess the impact of the Suez crisis in March 2021, and target a **15% reduction in cargo turnaround time**.

> **This dataset was created for the Onyx Data DNA challenge and does not reflect actual terminal operations.** All trends are based on a modeling exercise and do not represent the real cargo network. Please review the [Data Limitations](#3-data-limits-and-assumptions) section before using these figures in any business context.

> **Project Brief & Data Dictionary:** See [`CHALLENGE_BRIEF.md`](docs/CHALLENGE_BRIEF.md) & [`DATA_DICTIONARY.md`](docs/DATA_DICTIONARY.md).

---

## 1. Business Problem and Objectives

Improve the allocation of terminals and cut down on cargo handling delays on all global trade routes.

* **Primary Objective:** improve the way terminals are allocated so that the time taken for cargo movements is reduced by 15%.
* **Core Objectives:**
  * **Suez Impact:** investigate the impact on Suez, specifically, identify the disruption timeline for the first quarter of 2021, the spikes in volume by region (EMEA, APAC, and AMER), and the length of time it took to recover.
  * **Infrastructure Bottlenecks:** Analyze the relationship between terminal usage and move duration to identify which terminals need capacity increases.
  * **Efficiency Anomalies:** Benchmark vessel performance and isolate primary delay drivers (vessel age, location, shift timing).
* **Data Source:** [Onyx Data DNA Challenge (Mar–Apr 2026)](https://datadna.onyxdata.co.uk/challenges/march-april-2026-datadna-international-maritime-logistics-terminal-efficiency-analytics-challenge/)

---

## 2. Dataset & Architecture

Star Schema data model connecting 4 relational CSV tables to fact_cargo_movements:

| Table | File | Rows | Description |
|---|---|---|---|
| **Fact Movements** | `fact_cargo_movements.csv` | 15,000 | Handling events & durations (hours) |
| **Dim Terminal** | `dim_terminal.csv` | 50 | Terminal metadata & regional hubs |
| **Dim Vessel** | `dim_vessel.csv` | 1,000 | Build year & vessel category |
| **Dim Time** | `dim_time.csv` | 1,461 | Calendar dates (2021–2024) & shifts |

---

## 3. Data Limits and Assumptions

The dataset was created for a data challenge and is not based on actual terminal operations data. Before treating any figures below as real KPIs, be aware of these five points about the raw files:

1. **Vessel build year data contains inaccuracies.** Although specifications require build years from 1990 to 2023, the file includes vessels dating back to 1900. Approximately 72% of the 1000 vessels are listed with build years before 1990. These unrealistic entries are grouped into an "Invalid" age band on page 4, "Drivers & Strategy," rather than being removed or estimated. Age-related findings, such as the +6.36% slowdown for older vessels, only include those built between 1990 and 2023.
2. **Route geometry provides no meaningful data.** All 15,000 rows in fact_cargo_movements share the same coordinate pair, so actual port-to-port routing is not represented. As a result, this data was excluded from the dashboard, and no lane or trade-route maps were created.
3. **Move duration is capped, not free-running.** move_duration ranges from ~0.03h to ~999.99h with a hard ceiling near 1,000h. Rows at or near that ceiling are data-generation artifacts, not stuck shipments or real delays. They should not be read as operational outliers. Anomaly checks exclude rows within 1h of the cap and flag durations outside the normal spread instead of sorting by highest raw hours.
4. **Terminal capacity is assumed, not measured.** The raw data lacks a terminal_capacity field, so "high risk" terminals on the Bottleneck page are not benchmarked against real berth or yard limits. Capacity is approximated as 120% of a terminal's average container load over the period. Any terminal running above that line is flagged as near or over effective capacity. Replace with real capacity figures (berth slots, yard TEU limit, crane-hours) if this model is rebuilt on live ops data.
5. **Year-over-year swings reflect the dataset, not the business.** YoY comparisons on the Overview page mostly track how synthetic rows are spread across 2021–2024, not real cargo growth. Treat them as a data-shape note, not a growth story.

---

## 4. Executive Summary & Key Insights

### 4.1. Suez Canal Disruption (Mar 2021)

* **Duration Impact:** Average handling duration increased by **13.00% (+63.15 hours)**, rising from a baseline of **485** hours to **549** hours.
* **Recovery Window:** The network required **8 weeks**, from March 30 to May 24, 2021, to return to normal operations.
* **Worst Hit Hubs:** **EMEA** experienced the largest delay increase (**+26.21% / +117.37 hours**), followed by **LATAM** (**+18.50% / +97.06 hours**).

### 4.2. Terminal Bottlenecks & Capacity

* **High-Risk Facilities:** 15 terminals, representing 30% of the network, operate in Quadrant 1 (*High Load and High Duration*, based on an **assumed capacity line** of 120% average terminal load; see [Data Limitations](#3-data-limits-and-assumptions)). Peak bottlenecks include **Angel Castillo** (EMEA, 518.6 hours), **Shawn Martinez** (APAC, 521.8 hours), **Lauren Little** (LATAM, 520.6 hours), and **Deborah Perez** (AMER, 530.1 hours).
* **Congestion Premium:** High-volume terminals (over **150,400 containers**) average **504.25 hours, which is 1.32% higher than low-volume terminals. Peak** facilities exceed the baseline by 5.8% or 30 hours.

### 4.3. Shift & Fleet Performance Drivers

* **Shift Variance:** **Day shifts average 505.72 hours, while night shifts average 496.81 hours, resulting in an 8.91-hour (1.76%) gap.**
* **Fleet Age Impact:** Vessels aged 11 to 20 years average 518.72 hours per move, which is 6.36% (31.02 hours) slower than vessels built in the last decade (**487.69 hours). This figure excludes** pre-1990 "Invalid" build-year entries as noted in [Data Limitations](#3-data-limits-and-assumptions).

---

## 5. Actionable Recommendations

1. **Volume Reallocation (~8% target reduction):** Shift cargo from bottleneck terminals (*Angel Castillo, Lauren Little*) to underutilized neighbor facilities (*Sara Sanders, Lisa Carter*).
2. **Shift Optimization (~5% target reduction):** Standardize night-shift equipment maintenance and staffing to close the 8.91-hour shift gap.
3. **Legacy Vessel Scheduling (target reduction):** Implement priority berth scheduling for vessels over 11 years in service to eliminate the 6.36% duration penalty.

---

## 6. Dashboard Overview

### Page 1: Overview
Executive summary, core KPIs, volume trends, regional splits.
![Overview Page](assets/01_overview%20Page.png)

### Page 2: Disruption (Suez Canal Impact)
Timeline analysis tracking weekly duration spikes and recovery compared to baseline metrics.
![Disruption Page](assets/02_disruption%20Page.png)

### Page 3: Bottleneck (Terminal & Allocation)
Two-axis quadrant scatter plot identifying high-risk terminals and a terminal-by-vessel heatmap matrix.
![Bottleneck Page](assets/03_bottleneck%20Page.png)

### Page 4: Drivers & Strategy
Detailed analysis of shift disparities, vessel age distribution, and regional hub deviations.
![Drivers & Strategy Page](assets/04_performance%20Insights.png)

---

## 7. Key DAX Logic

A flexible 4-quadrant approach to group and evaluate terminal performance:

```dax
-- Dynamic Quadrant Classification for Terminal Efficiency
Terminal Action = 
VAR _Load = [Total Containers]
VAR _AvgLoad = [Avg Containers per Terminal]
VAR _Duration = [Avg Move Duration]
VAR _NetworkDuration = [Network Avg Duration]
RETURN
SWITCH(
    TRUE(),
    _Load >= _AvgLoad && _Duration >= _NetworkDuration, "Capacity / Process Review",
    _Load >= _AvgLoad && _Duration < _NetworkDuration, "Efficient Core",
    _Load < _AvgLoad && _Duration >= _NetworkDuration, "Process Review",
    "Reallocation Candidate"
)
```

> **Full DAX Reference:** For detailed DAX formulas across all report pages, see [`DAX_DOCUMENTATION.md`](docs/DAX_DOCUMENTATION.md).

---

## 8. Repository Structure

```
├── assets/         # Dashboard page screenshots
├── data/           # Raw CSV relational datasets
├── docs/           # Business context, Data Dictionary & DAX Documentation
├── powerbi/        # Power BI report file (.pbix / .pbip)
└── README.md       # Project documentation
```

---

## 9. Acknowledgments & References

* **Challenge Host:** [Onyx Data DNA Dataset Challenge](https://onyxdata.co.uk/data-dna-dataset-challenge/)
* **Project Brief:** [Data DNA March/April 2026 - International Maritime Logistics Analytics Challenge](https://datadna.onyxdata.co.uk/challenges/march-april-2026-datadna-international-maritime-logistics-terminal-efficiency-analytics-challenge/)
