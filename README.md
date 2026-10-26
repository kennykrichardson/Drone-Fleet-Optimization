# 🚁 Drone Fleet Optimization

### From a single-drone pathfinding demo to a multi-drone delivery-planning prototype

**Drone Fleet Optimization** builds on the original `Project.ipynb` and takes the idea a step further: instead of planning deliveries around one drone, it introduces a small fleet, delivery assignment, payload limits, battery-budget checks, obstacle-aware routing, and a visual overview of planned routes.

The goal is to explore how **task allocation + route planning + resource constraints** fit together in a drone-delivery workflow. This is a simulation and an optimization prototype, not a production flight-control system.

---

## ✨ What’s in this repository?

| File | Purpose |
|---|---|
| `Project.ipynb` | 📘 Original single-drone project: the baseline implementation for comparison. |
| `DroneFleet_Optimizer.ipynb` | 🚀 Extended multi-drone fleet simulation with assignment logic and fleet visualization. |
| `README.md` | 🧭 Project overview, setup instructions, and comparison guide. |

## 🆚 Original project vs. improved version

The original notebook is a useful starting point: it establishes a map, delivery locations, obstacles, and RRT*-based route planning. The fleet version builds on that foundation and broadens the problem being simulated.

| Capability | `Project.ipynb` — original | `DroneFleet_Optimizer.ipynb` — fleet version |
|---|---|---|
| 🚁 Drone model | A single-drone delivery workflow | Multiple named drones with individual limits |
| 📦 Delivery assignment | Delivery points handled in a single-drone flow | Greedy heuristic assigns deliveries across the fleet |
| ⚖️ Payload constraints | One shared weight limit | Payload capacity tracked per drone |
| 🔋 Battery budget | No per-drone battery model in the original workflow | Battery budget checked during assignment and distance consumption tracked |
| 🗺️ Route planning | RRT*-style obstacle-aware path planning | RRT*-style planning used for drone-to-delivery routes |
| 🚫 Restricted areas | Buildings and no-fly-zone map concepts | Randomly generated buildings and no-fly zones, with route collision checks against no-fly zones |
| 📊 Fleet visibility | Single-drone route visualization | Color-coded routes for different drones and return legs |
| 📈 Results | Delivery-route simulation output | Fleet assignments, completed-delivery count, distance, and per-drone summaries |

**In short:** the original asks, “How can one drone navigate deliveries?” The extended notebook asks, “How can a fleet divide deliveries while considering capacity, battery budget, and route feasibility?”

> ⚠️ These are prototype-level improvements, not a guarantee of globally optimal assignments. The assignment strategy is greedy, and the simulation uses simplified distance-based battery accounting.

## 🧠 How it works

1. **🗺️ Generate a map** — create a 2D operating area with randomly placed buildings and no-fly zones.
2. **📍 Define deliveries** — set delivery coordinates and package weights.
3. **🚁 Create a fleet** — configure drones with different payload and battery limits.
4. **🧩 Assign tasks** — process heavier packages first, check candidate drones, and choose a feasible candidate based on estimated outbound and return distance.
5. **🧭 Plan routes** — use an RRT*-style planner to find paths that avoid restricted zones.
6. **📦 Simulate delivery runs** — calculate route distances, update the drone’s tracked battery and position, and record deliveries.
7. **📊 Visualize results** — plot delivery locations, obstacles, drone routes, and return-to-base paths.

### 🛠️ Main concepts used

- **RRT\*** (Rapidly-exploring Random Tree Star): sampling-based path planning with a rewiring step intended to improve path cost.
- **Greedy task allocation:** a lightweight heuristic that selects a feasible drone for each delivery rather than solving a global optimization problem.
- **Resource constraints:** payload and distance-based battery budgets.
- **Procedural environment generation:** randomized buildings and no-fly zones for repeatable simulation experiments.
- **Matplotlib visualization:** a 2D view of the simulated fleet and routes.

## 🧰 Tech stack

- 🐍 **Python**
- 📓 **Jupyter Notebook**
- 🔢 **NumPy** — vector calculations and Euclidean distance
- 📊 **Matplotlib** — plotting the environment and routes
- 🎲 **Python `random`** — map generation and planner sampling
- 🧱 **`dataclasses` and `math`** — drone state and utility logic

No machine-learning model is required. The current fleet assignment is a heuristic baseline, which makes it easier to inspect and improve before adding more complex optimization methods.

## 🚀 Getting started

### 1. Clone or download the repository

```bash
git clone <repository-url>
cd Drone-Fleet-Optimization
```

Replace `<repository-url>` with the actual URL of your Git repository.

### 2. Create an environment (recommended)

```bash
python -m venv .venv
```

Activate it:

**Windows PowerShell**
```powershell
.venv\Scripts\Activate.ps1
```

**macOS / Linux**
```bash
source .venv/bin/activate
```

### 3. Install dependencies

```bash
python -m pip install numpy matplotlib notebook
```

### 4. Launch Jupyter

```bash
jupyter notebook
```

Open **`DroneFleet_Optimizer.ipynb`** to run the fleet simulation. Open **`Project.ipynb`** to inspect the original baseline.

Run the notebook cells from top to bottom. The simulation prints assignments and summary metrics, then displays a Matplotlib route plot.

## ⚙️ Experiment with the simulation

The main configuration is near the top of `DroneFleet_Optimizer.ipynb`:

```python
MAP_SIZE = (100, 100)
STEP_SIZE = 8
MAX_ITER = 500
START = (0, 0)
```

You can also modify:

- 📍 `DELIVERY_POINTS` — delivery coordinates.
- ⚖️ `WEIGHTS` — package weights, in the simulation’s chosen weight units (the example labels them as kg).
- 🚁 `Drone(...)` — each drone’s `payload_limit` and `battery_limit`.
- 🏢 `generate_random_map(...)` — the number of buildings and no-fly zones.
- 🧭 `STEP_SIZE`, `MAX_ITER`, and the planner’s `radius` — route-planning behavior and computational effort.

The notebook seeds Python’s and NumPy’s random generators to make experiments more reproducible. Because route planning and map generation involve randomness, change the seeds to explore different scenarios.

## 📊 What the simulation reports

- 🧩 Which deliveries were assigned to each drone.
- 📦 Delivery completion count.
- 📏 Total simulated travel distance.
- 🔋 Tracked battery remaining for each drone.
- 🚁 Per-drone distance and delivery summaries.
- 🗺️ A plot of the environment and planned delivery routes.

These are **simulated results**, not measurements from real drones. The reported distance is in map-coordinate units, and the battery model is simplified rather than calibrated to a physical aircraft.

## 🔭 Potential next improvements

This version establishes a baseline for further work. Promising next steps include:

- 🧮 Replace greedy assignment with an optimization method such as integer programming, a genetic algorithm, or vehicle-routing heuristics.
- 🔋 Model battery consumption using payload, speed, wind, elevation, and reserve requirements.
- 🏠 Enforce return-to-base feasibility after every delivery and update assignment estimates as a route evolves.
- 🧱 Check every route segment against both buildings and no-fly zones using robust geometric collision tests.
- 🛠️ Improve RRT* parent selection and rewiring so path-cost updates remain consistent throughout the tree.
- 🔁 Add retries or alternate planners when a route cannot be found.
- 📉 Benchmark the fleet version against the original notebook across the same maps and delivery sets.
- 🧪 Add automated tests, structured configuration, logging, and saved experiment results.
- 🌦️ Introduce dynamic obstacles, weather, charging stations, and time windows.
- 🛰️ Integrate real map data only after the simulation’s safety and validation assumptions are clearly defined.

## ⚠️ Scope and limitations

This repository is an educational simulation. It does **not** connect to real drones, validate airspace rules, or provide flight-ready navigation.

The current implementation uses simplified assumptions, including:

- a 2D map and distance-based battery accounting;
- a greedy assignment heuristic rather than a globally optimal solver;
- randomly generated obstacles and no-fly zones;
- sampled collision checks, which may miss edge cases;
- simulated route planning rather than real-world localization, telemetry, or control.

Use it to study and improve planning logic, not to operate physical aircraft.

## 🤝 Contributing

Ideas, bug reports, and improvements are welcome. Useful contributions include better assignment algorithms, more reliable collision checking, tests, benchmarks, and clearer result reporting.

1. Fork the repository.
2. Create a feature branch.
3. Make a focused change and test the notebook.
4. Open a pull request describing the problem and the approach.

## 📄 License

No license file was included in the supplied project archive. Add a `LICENSE` file and update this section once the intended license has been chosen. Do not assume the project is MIT-licensed without confirming that choice.

---

<div align="center">

### 🚁 Smarter assignments. Safer simulated routes. Better fleet planning. 📦

*An iterative step from single-drone pathfinding toward multi-drone delivery optimization.*

</div>
