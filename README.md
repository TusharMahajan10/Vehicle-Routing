## Uncertainty-Aware Capacitated Vehicle Routing using Machine Learning and OR-Tools

This project presents an **end-to-end data-driven framework for solving the Capacitated Vehicle Routing Problem (CVRP) under travel-time uncertainty** using real-world traffic and road-network information from Bengaluru, India. Instead of treating travel time between locations as a deterministic quantity, the framework models its uncertainty through **quantile-based machine learning predictions** and evaluates routing decisions under optimistic, median, and conservative traffic scenarios.

The pipeline integrates **OpenStreetMap/OSRM road distances**, historical **Uber Movement travel-time data**, **XGBoost quantile regression**, and **Google OR-Tools** to generate capacity-constrained vehicle routes across 16 major Bengaluru zones.

### Technical Pipeline

1. **Road-Network Distance Modeling**
   Real driving distances between 16 representative Bengaluru zones are obtained using the OpenStreetMap OSRM routing API, producing a complete origin–destination distance matrix.

2. **Traffic Data Preprocessing & Feature Engineering**
   Historical Uber Movement data is mapped to the selected zones and processed to capture spatial, temporal, seasonal, and land-use characteristics. Features include source/destination zones, road distance, hour-of-day cyclic encoding, morning/evening peak indicators, monsoon seasonality, zone categories, and traffic-speed proxies.

3. **Probabilistic Travel-Time Prediction**
   Three **XGBoost quantile regression models** estimate the conditional travel-time distribution:

   * **Q10** — optimistic / low travel-time scenario
   * **Q50** — median / expected scenario
   * **Q90** — conservative / high travel-time scenario

   This produces uncertainty-aware origin–destination travel-time matrices rather than relying on a single deterministic travel-time estimate.

4. **Capacitated Vehicle Routing Optimization**
   The predicted travel-time matrices are integrated with **Google OR-Tools** to solve CVRP instances subject to vehicle-capacity constraints. The routing solver uses **Path Cheapest Arc** for initial solution construction followed by **Guided Local Search** for route improvement.

5. **Risk-Aware Routing Analysis**
   Identical demand instances are optimized independently using Q10, Q50, and Q90 travel-time matrices. This enables analysis of the trade-off between aggressive routing based on optimistic traffic conditions and more robust routing based on conservative travel-time estimates.

### Model Performance

The median **Q50 XGBoost model** achieves strong travel-time prediction performance on the held-out test set:

* **MAE:** 0.388 min
* **RMSE:** 0.583 min
* **MAPE:** 1.19%
* **Q10–Q90 Prediction Interval Coverage (PICP):** 73.0%
* **PINAW:** 0.0153

Feature-importance analysis indicates that **road distance, traffic-speed proxy, peak-hour conditions, and source/destination zone characteristics** are among the primary factors influencing predicted travel time.

### CVRP Configuration

The routing experiments consider **15 customer locations and one depot (Whitefield)** across Bengaluru, with a fleet of **4 vehicles**, each having a capacity of **100 units**. Multiple randomized demand instances are evaluated under each quantile-based travel-time scenario to quantify the impact of traffic uncertainty on routing cost.

### Key Idea

Traditional CVRP formulations generally optimize routes using fixed travel costs. This project extends that formulation by coupling **machine-learning-based probabilistic travel-time estimation with combinatorial route optimization**, allowing routing decisions to explicitly account for traffic uncertainty.

The resulting framework demonstrates a practical **predict-then-optimize pipeline** for data-driven urban logistics:

**Historical Traffic + Road Network → Feature Engineering → Quantile Travel-Time Prediction → Uncertainty-Aware Cost Matrices → CVRP Optimization → Risk-Aware Routes**

### Tech Stack

`Python` · `Pandas` · `NumPy` · `XGBoost` · `Scikit-learn` · `Google OR-Tools` · `OpenStreetMap` · `OSRM` · `Uber Movement`
