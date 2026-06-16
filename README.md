# Cfai-Project-Campus-Navigation-System-
# College Indoor Navigation System

A terminal-based indoor navigation system for a multi-floor college building, written in pure Python. Select your source and destination from a numbered list — no typing room names — and get clear, step-by-step directions through corridors, staircases, elevators, and lifts.

---

## Features

- **A\* pathfinding** with a floor-distance heuristic for fast, optimal routes
- **70+ locations** across 6 floors: Cellar, Ground, First, Second, Third, Fourth
- **Human-readable directions** — every edge carries a natural instruction like *"Turn left past the elevator to reach Boys Washroom"*
- **Numbered selection menus** — no manual typing of room names
- **Vertical travel support** — Elevator shaft and Lift 2 shaft across all floors
- **JSON export/import ready** — serialize the entire graph with `graph.to_dict()`

---

## Project Structure

```
navigation.py          # Single-file application — all classes and data in one place
README.md
```

### Class Overview

| Class | Responsibility |
|---|---|
| `Location` | Dataclass — holds `id`, `name`, `floor`, `type` |
| `Graph` | Adjacency-list graph, edge storage, A\* and Dijkstra routing |
| `NavigationSystem` | UI loop, location display, direction generation |

---

## Building Layout

| Floor | Key Locations |
|---|---|
| Cellar | HC01 IoT Lab, Workshop 1–3, Boys/Girls Common Room, Examination Branch, Rooms 14A–15D |
| Ground | Principal Office, Board Room, Lab 008, Rooms 003–007 |
| First | Rooms 101–107, Lab 108, FED HOD Room, Staff Room |
| Second | ECE HOD & Staff Room, Rooms 201–206, Library |
| Third | Auditorium Entrance, Lab H301/H303, CSIT HOD Room, Rooms 302–307 |
| Fourth | CSE HOD & Staff Room, Rooms 401–407, Auditorium Exit |

Vertical connections:
- **Main Elevator** — Cellar → Ground → First → Second → Third → Fourth
- **Lift 2** — Cellar → First → Second → Third
- **Staircase Set 1, 2, 3** — floor-by-floor staircase connections throughout

---

## Requirements

- Python 3.10 or later (uses `match`-free syntax; works on 3.8+ with dataclasses)
- No external libraries — pure standard library

---

## How to Run

```bash
python3 navigation.py
```

### Example Session

```
==================================================
     COLLEGE INDOOR NAVIGATION SYSTEM
==================================================

  Select Source Location

  [  1] Cellar Entrance / Elevator          [Cellar]
  [  2] Girls Common Room                   [Cellar]
  ...
  [ 68] Auditorium Exit                     [Fourth Floor]

Enter Source Number: 1

  Source Selected: Cellar Entrance / Elevator  [Cellar]

  Select Destination Location

Enter Destination Number: 57

==================================================
  ROUTE FOUND
==================================================

  Source      : Cellar Entrance / Elevator  [Cellar Floor]
  Destination : Auditorium Entrance  [Third Floor]

  Directions:

    1. Walk straight along the left corridor past the elevator to reach Girls Common Room.
    2. Continue straight along the corridor to reach Boys Common Room.
    ...
    8. Walk straight ahead — Auditorium Entrance is directly in front of you.
    9. You have arrived at your destination: Auditorium Entrance.

  ✔  Destination Arrived.
==================================================
```

---

## Node & Edge Format

Every location is stored as:

```json
{
  "id": 106,
  "name": "HC01 IoT Lab",
  "floor": "Cellar",
  "type": "Lab"
}
```

Every connection carries a cost and a navigation instruction:

```json
{
  "from": 105,
  "to": 106,
  "cost": 1,
  "instruction": "Continue straight to reach HC01 IoT Lab."
}
```

---

## Exporting the Graph as JSON

```python
from navigation import build_college_graph
import json

graph = build_college_graph()
with open("building.json", "w") as f:
    json.dump(graph.to_dict(), f, indent=2)
```

To load it back:

```python
from navigation import Graph
import json

with open("building.json") as f:
    data = json.load(f)

graph = Graph.from_dict(data)
```

---

## Extending the System

**Add a new room:**

```python
graph.add_location(Location(id=700, name="New Seminar Hall", floor="Fourth", type="Room"))
graph.add_edge(615, 700, 1, "Continue past the Auditorium Exit to the Seminar Hall.")
```

**Add a new floor:**  
Follow the same pattern — assign a new ID range, register locations, wire edges to the nearest staircase or elevator on the floor below.

**Load from JSON:**  
Swap `build_college_graph()` in `main()` with `Graph.from_dict(json.load(...))` to drive the building data entirely from a file.

---

## Algorithm

The system uses **A\*** with a floor-distance heuristic:

```
h(n) = |floor(n) − floor(goal)| × 0.5
```

This guides the search upward or downward toward the destination floor, reducing unnecessary exploration on wrong floors. The heuristic is admissible (never overestimates), so A\* always finds the optimal path.

---

## Course Outcomes Mapping

| CO | Description | Modules / Implementation in This Project |
|---|---|---|
| CO1 | Formulate real-world tasks as AI problems (state space, actions, transitions, goals, costs, constraints) and implement representations using Python + core data structures with traceable reasoning. | The college building is modelled as a **state-space graph** — locations are states, corridors/staircases/elevators are transitions, and the destination is the goal. Implemented using Python `dataclasses`, `dicts`, and adjacency lists (`Location`, `Graph`). Every node carries traceable attributes: `id`, `name`, `floor`, `type`. |
| CO2 | Implement and analyze graph/state-space search algorithms (BFS/DFS/UCS/A*/Greedy) and design heuristics with admissibility/consistency reasoning; empirically compare time/space trade-offs. | Implements **A\* search** (`Graph.astar`) with an admissible floor-distance heuristic `h(n) = \|floor(n) − floor(goal)\| × 0.5`, and **Dijkstra** (`Graph.dijkstra`) as a fallback. The heuristic never overestimates (admissible) and satisfies the consistency property across floor transitions. |
| CO6 | Build integrated AI reasoning pipelines in Python combining representation + search + CSP + uncertainty + decision logic, and produce explainable outputs (reasoning traces, constraint proofs, inference steps). | The `NavigationSystem` class combines **graph representation + A\* search + natural-language direction generation** into a single end-to-end pipeline. Every route produces an explainable step-by-step instruction trace (e.g., *"Turn left past the elevator…"*) rather than raw node IDs. Supports JSON export for full reasoning transparency. |

> **Note:** CO3 (CSP/backtracking), CO4 (minimax/adversarial search), and CO5 (Bayesian/Markov models) are outside the scope of a navigation system and are not covered by this project.

---

## License

MIT — free to use, modify, and extend.
