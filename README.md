# Anylogic Simulation: e-commerce model for warehouse truck carrier

# Baseline AnyLogic Simulation: Warehouse Selection & Order Fulfillment

## Overview

This simulation models a last-mile logistics network where **customer orders** are fulfilled by the **nearest warehouse** using either **main trucks** or **gig carriers**. The objective is to simulate and evaluate performance under a **distance-based warehouse selection strategy**, serving as a baseline for comparison with ML-enhanced models.

---

## Model Components

### Agents

| Agent        | Role                                                                 |
|--------------|----------------------------------------------------------------------|
| `Main`       | Root agent, stores simulation-wide parameters and agent populations. |
| `Customer`   | Triggers random orders at a defined rate.                             |
| `Warehouse`  | Represents physical facilities for shipment dispatch.                 |
| `Main_truck` | Standard delivery vehicle.                                            |
| `Gig_carrier`| On-demand delivery vehicle.                                           |
| `CustOrder`  | Represents the physical shipment being delivered.                     |

---

## Process Flow

1. **Order Placement**  
   - Each customer places orders at a stochastic rate (e.g., 10 per week, exponentially distributed).

2. **Warehouse Selection**  
   - The **nearest warehouse** is selected based on route or GIS distance.

3. **Carrier Assignment**  
   - Cost is computed for each available truck type:
     ```
     cost_main = 10 × weight + 5 × size + 3 × distance
     cost_gig  = 12 × weight + 2 × size + 1 × distance
     ```
   - The truck with **lower cost** is selected.

4. **Order Shipment**
   - The selected truck moves from the warehouse to the customer.
   - The `CustOrder` agent is teleported to the warehouse upon assignment.
   - Once delivered, the truck returns to the warehouse.

---

## Model Parameters

| Parameter       | Description                             |
|----------------|-----------------------------------------|
| `weight`        | Randomized between 1–50 kg              |
| `size`          | Randomized between 0.1–2.0 m³           |
| `distance`      | Route-based or haversine GIS distance   |
| `cost_main`     | Delivery cost for main truck            |
| `cost_gig`      | Delivery cost for gig carrier           |

---

## Outputs & Visualization

- **TimePlot** of order lead time (placement → delivery)
- **Bar Charts** comparing average delivery delay
- **Truck StateCharts** showing utilization states
- **Order & truck movement** on GIS map

---

## Simulation Setup

- **Order frequency**: 10 per week per customer (`λ = 10`, exponential)
- **Truck logic**: Modeled using statecharts with transitions triggered by `CustOrder` messages
- **Assignment logic**:  
  - Closest warehouse → cheapest carrier → immediate dispatch

---

## Assumptions

- All warehouses and trucks are always available
- No queuing, capacity limits, or dynamic traffic effects
- Distance is a proxy for time (1 km ≈ 1 unit time)


---

## Files & Structure

```bash
anylogic-baseline/
├── Main.als                # Main simulation model
├── Customer.java           # Customer agent logic
├── Main_truck.java         # Truck agent logic
├── Gig_carrier.java        # Gig carrier agent
├── CustOrder.java          # Order agent
├── README.md               # This file
```
---

# ML-Integrated AnyLogic Simulation: Warehouse Selection & Order Fulfillment

This script packages trained ML models and recent test data for integration with an **AnyLogic simulation** via Python Communicator.

## Python Code Setup and Assumptions:

- Your models are already trained and saved in the paths:
```
model/mean_reg_models/FEDEX+Ground_mean_reg_XGBoost_v3
model/mean_reg_models/UPS+Ground_mean_reg_XGBoost_v2
```
- The test data is saved in:
```
cleaned_merged_data_no_mismatch/{carrier}_test_df.parquet
```
- The FEATURE_COLS and SELECTED_CARRIERS are correctly defined in helper/utils.py.


---

## Overview

- Loads trained **XGBoost models** for selected carriers.
- Loads recent **test data** from your cleaned dataset.
- Encodes and packages the model, model structure, and data subset.
- Saves everything into a single `.pkl` payload file that AnyLogic can consume.

---


---

## How to Run

Make sure your environment includes:

- `pandas`
- `joblib`
- `xgboost`
- `sklearn`

Then run:

```bash
python generate_anylogic_payload.py



