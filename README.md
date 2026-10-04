# Artificial-Intelligence_project
3_1 AI course semester project

Videos:

https://drive.google.com/file/d/1qpj_agF8NwiFsydlkb7WYH4-4vH2I26L/view?usp=sharing

https://drive.google.com/file/d/1F-w2ALKxHJ3NPRq08hzMsRuUD4BAzeIX/view?usp=drive_link

#  Search Algorithms for FPGA Placement & Routing

**Course:** CSF 407 – Artificial Intelligence  
**Institution:** BITS Pilani, Hyderabad Campus  

---

##  Overview

This project explores the application of **AI-based metaheuristic search algorithms** to the **FPGA placement and routing problem** — a well-known NP-hard combinatorial optimisation challenge in digital design. The goal is to intelligently assign logic blocks to physical locations on an FPGA grid and route connections between them, minimising total wire length while respecting resource and obstacle constraints.

Five algorithms are implemented, benchmarked, and compared under a unified problem definition using **Manhattan distance-based wirelength** as the common objective metric.

---

##  Problem Statement

Field-Programmable Gate Arrays (FPGAs) require two critical design steps:

- **Placement** – Assigning computational logic blocks to physical grid cells on the chip.
- **Routing** – Establishing electrical connections between placed blocks through available routing tracks.

Both steps are tightly coupled and highly constrained by timing, power, resource utilisation, and physical layout. Traditional rule-based approaches fail to scale as designs grow in complexity — this project investigates AI-based search as an alternative.

---

##  Tech Stack

| Component | Technology |
|-----------|------------|
| Language | Python 3 |
| Numerical Computation | NumPy |
| Plotting & Analysis | Matplotlib |
| Live Visualisation | Tkinter (Canvas) |
| Grid & Obstacle Modelling | Custom classes |

---

##  Repository Structure

```
├── sa_modified_1.py          # Simulated Annealing
├── aco_modified_2.py         # Ant Colony Optimisation
├── ga_modified_1.py          # Genetic Algorithm
├── pso_modified.py           # Particle Swarm Optimisation
├── cuckoosearch_modified.py  # Cuckoo Search
├── Group25_Phase1_Final.docx # Phase 1 Report (Literature Survey & Plan)
└── README.md
```

---

##  Algorithms Implemented

### 1.  Genetic Algorithm (`ga_modified_1.py`)

Models FPGA candidate solutions as **chromosomes**, where each chromosome encodes:
- `placement` — a list of 2D coordinates for each logic block
- `routing_info` — a dictionary of edge-to-weight mappings representing routing paths

**Key Design Features:**
- **Three parent selection strategies** implemented and benchmarked:
  - `tournament` — selects the fittest from random sub-groups, injecting competitive pressure
  - `ranking` — weights selection by relative rank rather than raw fitness
  - `roulette_wheel` — probabilistic selection proportional to fitness score
- **One-point crossover** splits two parent chromosomes at the midpoint to produce a child
- **Dual-mode mutation** — independently mutates placement coordinates (random swap) and routing weights (random perturbation)
- **Fitness function** — inverse of combined placement spread and maximum routing edge weight (higher = better)
- **Live Tkinter canvas visualisation** — renders each chromosome as coloured numbered blocks, updating generation by generation so convergence is directly observable
- **Interactive comparison** — a `messagebox` pop-up surfaces fitness results for all three selection strategies at the end of each run

**Configuration:**
```
Population Size : 10
Generations     : 10
Mutation Rate   : 0.01
Num Nodes       : 6
Num Edges       : 20
```

---

### 2.  Simulated Annealing (`sa_modified_1.py`)

Models FPGA placement as blocks on a **5×5 grid**, with solution quality measured as total **Manhattan distance wirelength** across all block pairs.

**Key Design Features:**
- Accepts worse solutions probabilistically: `P = exp(-ΔW / T)`, allowing escape from local optima
- Block moves are applied via random single-step grid displacement (wrapping at boundaries)
- **Three cooling schedules** systematically compared: `0.95`, `0.98`, `0.99`
- Runs for **5,000 iterations** per schedule, with wirelength tracked at every step
- Matplotlib plots overlay all three cooling curves for direct comparison of exploration-exploitation dynamics

**Key Parameters:**
```
Initial Temperature : 1000.0
Cooling Rates       : [0.95, 0.98, 0.99]
Iterations          : 5000
Grid Size           : 5×5
```

---

### 3.  Ant Colony Optimisation (`aco_modified_2.py`)

Models the FPGA as a **3D pheromone grid** (`height × width × num_tracks`) where ant agents navigate a **10×10 chip surface** with randomly placed obstacles.

**Key Design Features:**
- `FPGA` class encapsulates the routing grid and tracks obstacle positions
- `Ant` agents start at random positions and move to valid adjacent cells (obstacle-aware)
- **Two pheromone update logics** compared:
  - `Logic 1` — stronger deposit rate (0.5), faster path reinforcement
  - `Logic 2` — weaker deposit rate (0.1), slower, more distributed exploration
- Both logics share the same evaporation rate (0.1)
- **Two colony-vs-iteration configurations** studied side by side:
  - **Case 1:** 20 ants × 50 iterations (breadth-first, fast saturation)
  - **Case 2:** 5 ants × 200 iterations (depth-first, slow reinforcement)
- Matplotlib convergence plots and Matplotlib `Rectangle` patch visualisations show pheromone density and final placements for all four combinations

**Key Parameters:**
```
Grid Size        : 10×10
Routing Tracks   : 4
Obstacles        : 5 (randomly placed)
Deposit Rates    : 0.5 (Logic 1), 0.1 (Logic 2)
Evaporation Rate : 0.1
```

---

### 4.  Particle Swarm Optimisation (`pso_modified.py`)

Models FPGA block positions as **particles** navigating a continuous 2D solution space, guided by their own best-known position and the global swarm best.

**Key Design Features:**
- Each `Particle` has a position (list of 2D node coordinates), velocity, and personal best
- Velocity update follows the standard PSO rule with tuned inertia, cognitive, and social weights
- `PSO` class tracks the global best across all particles and all iterations
- **Live Tkinter canvas** renders particle positions frame by frame, with each particle colour-coded
- Final best placement and fitness displayed via `messagebox`

**Key Parameters:**
```
Particles       : 5
Nodes           : 3
Max Iterations  : 10
Inertia Weight  : 0.7
Cognitive Weight: 1.5
Social Weight   : 1.5
```

---

### 5.  Cuckoo Search (`cuckoosearch_modified.py`)

Encodes FPGA placement as **nests**, where each nest is a matrix mapping nodes to grid cells.

**Key Design Features:**
- **Two behavioural update rules** compared:
  - `alpha` path — swaps two random node assignments (Lévy-flight proxy)
  - `beta` path — migrates a random node to a randomly selected empty cell
- Wirelength computed as sum of Manhattan distances between each node and its assigned cell
- Convergence curves for both cases plotted together for direct comparison
- Best nest and minimum wirelength reported for each case

**Key Parameters:**
```
Nodes      : 10
Cells      : 10
Alpha      : 0.5
Beta       : 0.5
Iterations : 100
```

---

##  Evaluation Metric

All five algorithms are evaluated using **Manhattan distance-based wirelength**:

$$W = \sum_{i} \sum_{j \neq i} (|x_i - x_j| + |y_i - y_j|)$$

This provides a hardware-grounded, physically interpretable measure of routing cost that is consistent across all algorithmic paradigms.

**Overall placement accuracy achieved: 80.5% – 90.2%**

---

## Running the Code

Each algorithm is a standalone script. Run any of them directly:

```bash
# Simulated Annealing
python sa_modified_1.py

# Genetic Algorithm (opens Tkinter GUI)
python ga_modified_1.py

# Ant Colony Optimisation
python aco_modified_2.py

# Particle Swarm Optimisation (opens Tkinter GUI)
python pso_modified.py

# Cuckoo Search
python cuckoosearch_modified.py
```

**Dependencies:**
```bash
pip install numpy matplotlib
# tkinter is included with standard Python distributions
```

>  `ga_modified_1.py` and `pso_modified.py` require a display environment (Tkinter GUI). Run in a desktop environment or use a virtual display (e.g., Xvfb) on headless servers.

---

## Key Findings

| Algorithm | Key Variable Studied | Observation |
|-----------|----------------------|-------------|
| Simulated Annealing | Cooling rate (0.95 / 0.98 / 0.99) | Slower cooling yields better final wirelength at the cost of more iterations |
| Genetic Algorithm | Selection strategy (tournament / ranking / roulette) | Tournament selection converges fastest; roulette-wheel is most explorative |
| Ant Colony Optimisation | Deposit rate × colony configuration | High deposit + more ants converges quickly; low deposit + more iterations finds more distributed solutions |
| Particle Swarm Optimisation | Inertia / cognitive / social weights | Balanced weights prevent premature convergence |
| Cuckoo Search | Update rule (swap vs. migration) | Node-swap updates converge more reliably on wire-length minimisation |

---

##  Report

The Phase 1 report (`Group25_Phase1_Final.docx`) covers:
- Introduction to FPGA routing and placement
- Literature survey across 15+ papers on AI-based placement techniques
- Research gap analysis
- Algorithmic plan and comparative framework design

---

##  References

Key literature surveyed includes works on:
- VPR (Versatile Place and Route) framework
- ACO-based FPGA routing (Minimax Ant System adaptations)
- Genetic Algorithm applications in VLSI placement
- Simulated Annealing for timing-driven placement
- Swarm intelligence for combinatorial optimisation
--
