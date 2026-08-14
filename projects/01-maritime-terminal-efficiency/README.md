# International Maritime Logistics & Terminal Efficiency Analysis

An interactive 4-page Power BI dashboard analyzing 15,000 cargo movement events across 50 global terminals and 1,000 vessels (2020–2023). Built to identify operational bottlenecks, quantify the March 2021 Suez blockage impact, analyze shift/vessel age drivers, and target a **15% reduction in cargo turnaround duration**.

> 📌 **Project Brief & Requirements:** For full background, business context, and challenge goals, see [`CHALLENGE_BRIEF.md`](docs/CHALLENGE_BRIEF.md).

---

## Dataset

Powered by 4 relational CSV tables in `data/`:

| Table | File | Rows | Primary Key | Description |
|---|---|---|---|---|
| **Fact Movements** | `fact_cargo_movements.csv` | 15,000 | `movement_id` | Cargo handling events & durations (hours) |
| **Dim Terminal** | `dim_terminal.csv` | 50 | `terminal_id` | Terminal metadata & regional hubs |
| **Dim Vessel** | `dim_vessel.csv` | 1,000 | `vessel_key` | Vessel specifications & build year |
| **Dim Time** | `dim_time.csv` | 1,461 | `date_id` | Calendar dates (2020–2023) & shift types |

> 📌 **Data Dictionary:** For full field definitions and data types, see [`DATA_DICTIONARY.md`](docs/DATA_DICTIONARY.md).

---

## Data Model

Built on a **Star Schema** with 1:N single-direction relationships to `fact_cargo_movements`:

```
 [dim_terminal] (1) ──┐
 [dim_vessel]   (1) ──┼──> (N) [fact_cargo_movements] <── [_Measures]
 [dim_time]     (1) ──┘
```

- **`_Measures`**: Centralized DAX measure table for KPIs, disruption tracking, and dynamic quadrant logic.
- **`_Format_Measures`**: Handles dynamic YoY color formatting and conditional labels.

---

## Technical & Analytical Mechanics

### 1. Key Calculated Columns
- **`Vessel Age Band`**: Segregates fleet age (`0-4 Yrs`, `5-9 Yrs`, `10-14 Yrs`, `15-19 Yrs`, `20+ Yrs`) to isolate legacy vessel delay impact.
- **`Suez Phase`**: Categorizes records into chronological windows (`Pre-Disruption`, `Blockage Event`, `Recovery Phase`, `Outside Window`) for disruption isolation.

### 2. Core DAX Measures
- **`Avg Move Duration`**: `AVERAGE(fact_cargo_movements[move_duration])` (Key operational target metric).
- **`Target Avg Duration`**: `[Avg Move Duration] * 0.85` (Sets 15% reduction baseline).
- **`Suez Duration Impact %`**: Calculates % spike in average duration during the blockage week (March 23–29, 2021) vs pre-event baseline.
- **`Night vs Day Gap`**: `[Night Avg Duration] - [Day Avg Duration]` to measure shift duration disparity.

### 3. Dynamic Bottleneck Action Logic
Categorizes 50 terminals into 4 operational quadrants based on volume load vs duration:
```dax
Terminal Action = 
IF([Total Containers] >= [Avg Containers per Terminal] && [Avg Move Duration] >= [Network Avg Duration], "Capacity / Process Review",
IF([Total Containers] >= [Avg Containers per Terminal], "Efficient Core",
IF([Avg Move Duration] >= [Network Avg Duration], "Process Review", "Reallocation Candidate")))
```

---

## Report Pages Overall

### Page 1: Overview
Network executive summary with KPI cards (`Total Containers`, `Total Movements`, `Avg Duration`, `Containers/Day`), monthly volume trends, and regional hub splits.

![Overview Page](assets/01_overview.png)

---

### Page 2: Disruption (Suez Canal Blockage & Recovery)
Timeline analysis tracking weekly duration spikes before, during, and after the March 2021 Suez crisis against baseline metrics.

![Disruption Page](assets/02_disruption.png)

---

### Page 3: Bottleneck (Terminal Bottleneck & Allocation)
2-axis Quadrant Scatter Plot (`Terminal Load vs Duration`) identifying high-risk terminals alongside a Terminal × Vessel category heatmap matrix.

![Bottleneck Page](assets/03_bottleneck.png)

---

### Page 4: Drivers & Strategy
Deep-dive into shift disparities (Day vs Night), vessel age impacts, and regional deviations vs global baselines to formulate optimization strategies.

![Drivers & Strategy Page](assets/04_drivers_strategy.png)

---

## Skills Demonstrated

- **Logistics & Supply Chain Modeling**: Star Schema data architecture & operational KPI engineering.
- **Advanced DAX**: Time Intelligence, scenario baselining, dynamic quadrant classification (`SWITCH`).
- **Data UX/UI Design**: Default 1280x720 canvas layout, dark maritime theme, and interactive page navigation.
