# DAX Measures & Analytics Documentation

Centralized documentation of core DAX measures, time-intelligence formulas, and dynamic business logic used across the 4-page Power BI dashboard.

---

## 1. Core KPIs & Network Benchmarks (Page 1: Overview)

### `Avg Move Duration`
Key operational target metric tracking mean container handling duration in hours.
```dax
Avg Move Duration = 
AVERAGE(fact_cargo_movements[move_duration])
```

### `Target Avg Duration`
Calculates the 15% duration reduction target baseline.
```dax
Target Avg Duration = 
[Avg Move Duration] * 0.85
```

### `Containers per Day`
Normalizes container throughput against operational movement hours.
```dax
Containers per Day = 
DIVIDE(
    [Total Containers],
    [Total Movement Hours] / 24
)
```

---

## 2. Disruption & Event Isolation (Page 2: Disruption)

### `Suez Pre Avg Duration`
Establishes the pre-disruption baseline (Jan 26 – Mar 22, 2021) excluding current filter contexts.
```dax
Suez Pre Avg Duration = 
CALCULATE(
    [Avg Move Duration],
    REMOVEFILTERS(dim_time),
    dim_time[date_id] >= DATE(2021, 1, 26),
    dim_time[date_id] <= DATE(2021, 3, 22)
)
```

### `Suez Event Avg Duration`
Calculates mean duration during the peak blockage week (Mar 23 – Mar 29, 2021).
```dax
Suez Event Avg Duration = 
CALCULATE(
    [Avg Move Duration],
    REMOVEFILTERS(dim_time),
    dim_time[date_id] >= DATE(2021, 3, 23),
    dim_time[date_id] <= DATE(2021, 3, 29)
)
```

### `Suez Duration Impact %`
Quantifies percentage spike in handling duration during the crisis versus pre-event baseline (+13.00%).
```dax
Suez Duration Impact % = 
DIVIDE(
    [Suez Event Avg Duration] - [Suez Pre Avg Duration],
    [Suez Pre Avg Duration]
)
```

---

## 3. Bottleneck & Quadrant Classification (Page 3: Bottleneck)

### `Avg Containers per Terminal`
Dynamic threshold calculating average volume across active terminals for quadrant segmentation.
```dax
Avg Containers per Terminal = 
AVERAGEX(
    VALUES(dim_terminal[terminal_id]),
    [Total Containers]
)
```

### `Network Avg Duration`
Global benchmark line preserved across terminal slicers using `ALLSELECTED`.
```dax
Network Avg Duration = 
CALCULATE(
    [Avg Move Duration],
    ALLSELECTED(dim_terminal)
)
```

### `Terminal Action` (Quadrant Classification)
Categorizes 50 terminals into 4 operational action quadrants based on volume load vs. duration.
```dax
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

---

## 4. Operational Drivers (Page 4: Drivers & Strategy)

### `Day Avg Duration` & `Night Avg Duration`
Shift-specific average movement duration.
```dax
Day Avg Duration = 
CALCULATE([Avg Move Duration], dim_time[shift] = "Day")

Night Avg Duration = 
CALCULATE([Avg Move Duration], dim_time[shift] = "Night")
```

### `Night vs Day Gap`
Evaluates shift duration disparity (-8.91 hours / 1.76% variance).
```dax
Night vs Day Gap = 
[Night Avg Duration] - [Day Avg Duration]
```
