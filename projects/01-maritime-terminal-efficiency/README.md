# International Maritime Logistics & Terminal Efficiency Analysis

A Power BI dashboard analyzing 15,000 cargo movement events across 50 global terminals and 1,000 vessels (2020–2023). Designed to identify bottlenecks, quantify the March 2021 Suez crisis impact, and target a **15% reduction in cargo turnaround duration**.

> 📌 **Project Brief & Data Dictionary:** See [`CHALLENGE_BRIEF.md`](docs/CHALLENGE_BRIEF.md) & [`DATA_DICTIONARY.md`](docs/DATA_DICTIONARY.md).

---

## 1. Business Problem & Objectives

Optimize terminal allocation and reduce cargo handling delays across global trade routes.
## 1. Business Problem & Objectives

* **Primary Objective:** Optimize terminal allocation to reduce cargo movement duration by **15%**.
* **Core Objectives:**
  * **Suez Impact:** Pinpoint Q1 2021 disruption timeline, regional volume spikes (EMEA/APAC/AMER), and recovery duration.
  * **Infrastructure Bottlenecks:** Analyze terminal utilization vs. move duration to identify priority terminals for capacity expansion.
  * **Efficiency Anomalies:** Benchmark vessel performance and isolate primary delay drivers (vessel age, location, shift timing).
* **Data Source:** [Onyx Data DNA Challenge (Mar–Apr 2026)](https://datadna.onyxdata.co.uk/challenges/march-april-2026-datadna-international-maritime-logistics-terminal-efficiency-analytics-challenge/)

---

## 2. Dataset & Architecture

Star Schema data model connecting 4 relational CSV tables to `fact_cargo_movements`:

| Table | File | Rows | Key Metric / Description |
|---|---|---|---|
| **Fact Movements** | `fact_cargo_movements.csv` | 15,000 | Handling events & durations (hours) |
| **Dim Terminal** | `dim_terminal.csv` | 50 | Terminal metadata & regional hubs |
| **Dim Vessel** | `dim_vessel.csv` | 1,000 | Build year & vessel category |
| **Dim Time** | `dim_time.csv` | 1,461 | Calendar dates (2020–2023) & shifts |

---

## 3. Executive Summary & Key Insights

### 3.1. Suez Canal Disruption (Mar 2021)
* **Duration Impact:** Average handling duration spiked by **+13.00% (+63.15h)**, from **485.86h** baseline to **549.00h**.
* **Recovery Window:** Network required **8 weeks** (Mar 30 – May 24, 2021) to normalize.
* **Worst Hit Hubs:** **EMEA** suffered the highest delay spike (**+26.21% / +117.37h**), followed by **LATAM** (**+18.50% / +97.06h**).

### 3.2. Terminal Bottlenecks & Capacity
* **High-Risk Facilities:** **15 terminals (30% of network)** operate in Quadrant 1 (*High Load & High Duration*). Peak bottlenecks: **Angel Castillo** (EMEA, 518.6h), **Shawn Martinez** (APAC, 521.8h), **Lauren Little** (LATAM, 520.6h), **Deborah Perez** (AMER, 530.1h).
* **Congestion Premium:** High-volume terminals (**>150.4k containers**) average **504.25h** (+1.32% vs low-volume; peak facilities exceed baseline by **+5.8% / +30h**).

### 3.3. Shift & Fleet Performance Drivers
* **Shift Variance:** Day shifts average **505.72h** vs. Night shifts at **496.81h** (**8.91h / 1.76% gap**).
* **Fleet Age Impact:** Vessels built before **2005** (11–20 yr age band: **518.72h**) take **+6.36% (+31.02h)** longer than modern vessels built post-2015 (**487.69h**).

---

## 4. Actionable Recommendations

1. **Volume Reallocation (~8% target reduction):** Shift cargo from bottleneck terminals (*Angel Castillo, Lauren Little*) to underutilized neighbor facilities (*Sara Sanders, Lisa Carter*).
2. **Shift Optimization (~5% target reduction):** Standardize night shift equipment maintenance and staffing to bridge the 8.91h shift gap.
3. **Legacy Vessel Scheduling (~2% target reduction):** Implement priority berth scheduling for legacy vessels (pre-2005) to eliminate the +6.36% duration penalty.

---

## 5. Dashboard Overview

### Page 1: Overview
Executive summary, core KPIs, volume trends, regional splits.
![Overview Page](assets/01_overview%20Page.png)

### Page 2: Disruption (Suez Canal Impact)
Timeline analysis tracking weekly duration spikes & recovery against baseline metrics.
![Disruption Page](assets/02_disruption%20Page.png)

### Page 3: Bottleneck (Terminal & Allocation)
2-axis Quadrant Scatter Plot identifying high-risk terminals & Terminal x Vessel heatmap matrix.
![Bottleneck Page](assets/03_bottleneck%20Page.png)

### Page 4: Drivers & Strategy
Deep-dive into shift disparities, vessel age distribution, and regional hub deviations.
![Drivers & Strategy Page](assets/04_performance%20Insights.png)

---

## 6. Key DAX Logic

Dynamic 4-quadrant categorization logic for terminal performance:

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

> 📌 **Full DAX Reference:** For detailed DAX formulas across all report pages, see [`DAX_DOCUMENTATION.md`](docs/DAX_DOCUMENTATION.md).

---

## 7. Repository Structure

```
├── assets/         # Dashboard page screenshots
├── data/           # Raw CSV relational datasets
├── docs/           # Business context, Data Dictionary & DAX Documentation
├── powerbi/        # Power BI report file (.pbix / .pbip)
└── README.md       # Project documentation
```

---

## 8. Acknowledgements & References

* **Challenge Host:** [Onyx Data DNA Dataset Challenge](https://onyxdata.co.uk/data-dna-dataset-challenge/)
* **Project Brief:** [Data DNA March/April 2026 - International Maritime Logistics Analytics Challenge](https://datadna.onyxdata.co.uk/challenges/march-april-2026-datadna-international-maritime-logistics-terminal-efficiency-analytics-challenge/)
