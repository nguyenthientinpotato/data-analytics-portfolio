# International Maritime Logistics & Terminal Efficiency Analysis
A Power BI dashboard analyzing 15,000 cargo movement events across 50 global terminals and 1,000 vessels (2020–2023). Designed to identify operational bottlenecks, quantify the March 2021 Suez blockage impact, analyze performance drivers, and establish a data-driven path to reduce cargo turnaround duration by 15%.
=======
A Power BI dashboard analyzing 15,000 cargo movement events across 50 global terminals and 1,000 vessels (2020–2023). Built to identify operational bottlenecks, quantify the March 2021 Suez blockage impact, analyze shift/vessel age drivers, and target a **15% reduction in cargo turnaround duration**.

> 📌 **Project Brief & Requirements:** For full background, business context, and challenge goals, see [`CHALLENGE_BRIEF.md`](docs/CHALLENGE_BRIEF.md).

---
## 1. Business Problem & Objectives
Following global trade disruptions and uneven terminal workloads, Global Maritime Solutions needs operational clarity to optimize terminal allocation and reduce cargo handling delays.

* **Data Source:** [Onyx Data DNA Challenge (March - April 2026)](https://datadna.onyxdata.co.uk/challenges/march-april-2026-datadna-international-maritime-logistics-terminal-efficiency-analytics-challenge/)
* **Primary Goal:** Achieve a 15% reduction in overall cargo turnaround duration.
* **Core Analytical Questions:**
  1. **Suez Disruption Impact:** How severe was the March 2021 blockage, which hubs suffered most, and how long was the recovery window?
  2. **Infrastructure Bottlenecks:** Which terminals operate beyond optimal capacity and create severe network delays?
  3. **Efficiency Drivers:** How do shift timings (Day vs. Night), regional hubs, and vessel age drive movement duration?

---

## 2. Dataset & Architecture

Analyzed by 4 relational CSV datasets in `data/`:

| Table | File | Rows | Primary Key | Description |
|---|---|---|---|---|
| **Fact Movements** | `fact_cargo_movements.csv` | 15,000 | `movement_id` | Cargo handling events & durations (hours) |
| **Dim Terminal** | `dim_terminal.csv` | 50 | `terminal_id` | Terminal metadata & regional hubs |
| **Dim Vessel** | `dim_vessel.csv` | 1,000 | `vessel_key` | Vessel specifications & build year |
| **Dim Time** | `dim_time.csv` | 1,461 | `date_id` | Calendar dates (2020–2023) & shift types |

* **Data Model:** Star Schema (1:N single-direction relationships connected to `fact_cargo_movements`).
* **Documentation:** Detailed data dictionary available in [`DATA_DICTIONARY.md`](docs/DATA_DICTIONARY.md) and challenge scope in [`CHALLENGE_BRIEF.md`](docs/CHALLENGE_BRIEF.md).

---

## 3. Executive Summary & Key Insights

### 3.1. Disruption Analysis (Suez Canal Blockage)
* **Impact:** Average handling duration spiked by **+13.00% (+63.15 hours)**, rising from the pre-disruption baseline of **485.86 hours** to **549.00 hours** during the Suez Canal blockage week (March 23–29, 2021).
* **Recovery:** Network operations required an **8-week recovery window** (March 30 to May 24, 2021) to normalize to baseline efficiency. The **EMEA** regional hub suffered the worst disruption with a **+26.21% (+117.37 hours)** delay spike, followed by **LATAM** (**+18.50% / +97.06 hours**).

### 3.2. Terminal Bottlenecks & Capacity
* **High-Risk Terminals:** Identified **15 key terminals** (30% of total network) operating in the High Duration & High Load quadrant (*Quadrant 1*). Top bottleneck facilities include **Angel Castillo** (EMEA: 168.6k containers, 518.62h), **Shawn Martinez** (APAC: 159.8k containers, 521.80h), **Lauren Little** (LATAM: 159.8k containers, 520.57h), and **Deborah Perez** (AMER: 156.0k containers, 530.08h).
* **Congestion Correlation:** Terminals exceeding average network volume (**>150,400 containers**) experienced an average handling duration of **504.25 hours** (+1.32% overall congestion premium, with peak bottleneck facilities exceeding baseline by up to **+5.8% / +30 hours**).

### 3.3. Operational Drivers (Shifts & Vessel Age)
* **Shift Variance:** Day shifts currently average a handling duration of **505.72 hours** vs. Night shifts at **496.81 hours** (**8.91 hours / 1.76% variance**), reflecting peak daytime berth congestion and shift-level productivity gaps.
* **Vessel Fleet Impact:** Legacy vessels built before **2005** (specifically the 11–20 year age band averaging **518.72 hours**) require **+6.36% (+31.02 hours)** longer move durations compared to modern container ships built after 2015 (**487.69 hours**).

---

## 4. Actionable Recommendations & Targeted Impact

1. **Dynamic Fleet & Volume Reallocation:** Reallocate container volume from high-risk bottleneck terminals (**Angel Castillo**, **Lauren Little**, **Shawn Martinez**) to underutilized neighboring facilities within the same hub, such as **Sara Sanders / Andrew Baldwin** (EMEA) and **Lisa Carter / David Dixon** (LATAM). *(Targeted impact: ~8% duration reduction)*.
2. **Night Shift Operational Optimization:** Standardize equipment maintenance routines and staffing levels during night shifts to bridge the **8.91-hour** shift variance gap and balance 24/7 terminal throughput. *(Targeted impact: ~5% duration reduction)*.
3. **Vessel Priority Docking:** Implement priority scheduling and optimized berth allocation for legacy vessels (built before 2005 / 11-20+ years old) to minimize berth occupancy time and eliminate the **+6.36% (+31 hours)** duration penalty. *(Targeted impact: ~2% duration reduction)*.

---

## 5. Dashboard Overview

### Page 1: Overview
Executive summary displaying overall network KPIs (`Total Containers`, `Avg Duration`, `Containers/Day`), monthly volume trends, and regional hub breakdowns.
![Overview Page](assets/01_overview%20Page.png)

### Page 2: Disruption (Suez Canal Impact)
Timeline analysis tracking weekly duration spikes before, during, and after the March 2021 crisis against baseline metrics.
![Disruption Page](assets/02_disruption%20Page.png)

### Page 3: Bottleneck (Terminal & Allocation)
2-axis Quadrant Scatter Plot (`Terminal Load vs. Duration`) identifying bottlenecked terminals alongside a Terminal x Vessel category heatmap matrix.
![Bottleneck Page](assets/03_bottleneck%20Page.png)

### Page 4: Drivers & Strategy
Deep-dive analysis into shift disparities (Day vs. Night), vessel age distribution, and regional hub deviations.
![Drivers & Strategy Page](assets/04_performance%20Insights.png)

---

## 6. Key DAX Logic

Core dynamic quadrant classification used to categorize terminals into operational action categories:

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

> 📌 **Full DAX Reference:** For detailed DAX formulas across all report pages (disruption baselines, shift gaps, target metrics), see [`DAX_DOCUMENTATION.md`](docs/DAX_DOCUMENTATION.md).

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
* **Project Brief:** [Data DNA March/April 2026 - International Maritime Logistics & Terminal Efficiency Analytics Challenge](https://datadna.onyxdata.co.uk/challenges/march-april-2026-datadna-international-maritime-logistics-terminal-efficiency-analytics-challenge/)
