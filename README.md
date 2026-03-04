# Note: This project has been maintained as a hobby project beyond the completion of the Internship
---
# Satisfaction-Aware Task Assignment Problem (SATA)

## Table of Contents
- [Introduction](#introduction)
- [Project Structure](#project-structure)
- [Approaches](#approaches)
- [Installation](#installation)
- [Usage](#usage)

## Introduction
The Task-Worker Assignment Optimization project aims to solve the task-worker assignment problem by maximizing the overall satisfaction score. The problem involves assigning workers to tasks based on various constraints like location (spatial), skills, range, budget, and travel costs. 

This repository implements two core approaches: **Conflict-Aware Greedy (CAG)** and **Game-Theoretic (GT)** algorithm with threshold stop optimization. It handles both **deterministic** scenarios (complete worker info) and **probabilistic** scenarios (incomplete worker skill info via Monte Carlo sampling).

**Key Features:**
- **Modular Codebase**: Clean structure with separate packages for algorithms, utilities, and data.
- **Monte Carlo Simulation**: Scalable probabilistic calculation (replaces exponential brute-force).
- **Full Nash Equilibrium**: GT algorithm supports Join, Switch, and Leave moves.
- **Comprehensive Tests**: Extensive test suite for verification.
- **Visualization Scripts**: Ready-to-run experiment scripts with matplotlib plotting.

## Project Structure

```
Game-theoretic-approach-to-the-SATA-problem/
├── src/                            # Core Library
│   ├── common/                     # classes.py, distance.py, utils.py
│   ├── algorithms/                 # greedy.py, gt.py
│   └── probabilistic/              # monte_carlo.py
│
├── data/
│   ├── generators/
│   │   ├── gen_data.py             # Generates random task/worker data
│   │   ├── populate_csv.py         # Writes generated data to CSVs
│   │   ├── gen_input.py            # Loads CSVs into memory
│   │   └── start.py                # Helper functions
│   └── raw/                        # 9 CSV files (task_*, worker_*)
│
├── experiments/
│   ├── deterministic/              # effect_of_budget.py, etc.
│   ├── probabilistic/              # main.py, effect_of_monte_carlo.py
│   └── run_all.py                  # Runs all experiments
│
├── tests/
│   └── test_sanity.py
└── README.md
```


## Approaches

### 1. Conflict-Aware Greedy (CAG)
Iteratively assigns workers to tasks based on maximum satisfaction increment. Resolves conflicts when multiple tasks compete for the same worker by comparing satisfaction losses.

### 2. Game-Theoretic (GT) Algorithm
Builds upon CAG's initial assignment. Workers proactively optimize their assignments through:
- **Join**: Unassigned worker joins a task
- **Switch**: Worker moves from one task to another
- **Leave**: Worker becomes unassigned

The process continues until a **Nash Equilibrium** is reached (no worker can improve by unilaterally changing).

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/RoyCoding8/Game-theoretic-approach-to-the-SATA-problem.git
   cd Game-theoretic-approach-to-the-SATA-problem
   ```

2. Ensure **Python 3.8+** is installed.

3. Install dependencies:
   ```bash
   pip install pandas matplotlib
   ```

## Usage

### Run Tests
```bash
# Quick sanity check
python tests/test_sanity.py
```

### Run Individual Experiments
```bash
# Effect of budget on performance
python experiments/deterministic/effect_of_budget.py

# Effect of worker count
python experiments/deterministic/effect_of_workers.py

# Monte Carlo probabilistic experiment
python experiments/probabilistic/effect_of_monte_carlo.py
```

### Run All Experiments
```bash
python experiments/run_all.py
```
This generates plots saved as PNG files in the respective experiment folders.

### Probabilistic Main Entry Point
```bash
python experiments/probabilistic/main.py
```
Runs 500 Monte Carlo simulations and outputs expected satisfaction scores.

## Output
Each experiment script:
- Prints detailed results to console
- Generates 3 matplotlib plots:
  - Assignments vs. Variable
  - Satisfaction Score vs. Variable
  - Runtime vs. Variable
- Saves plots as PNG files in the experiment folder

## Custom Input Data

### Option 1: Generate Random Data
```bash
python data/generators/populate_csv.py   # Generate random data -> write CSVs
python experiments/run_all.py             # Run experiments with that data
```
Edit `data/generators/gen_data.py` to change N_TASKS, N_WORKERS, BUDGET ranges, etc.

### Option 2: Create Your Own CSVs

Place these 9 files in `data/raw/`:

| File | Columns |
|------|---------|
| `task_budget.csv` | task, budget |
| `task_location.csv` | task, latitude, longitude |
| `task_skills.csv` | task, req_skill (one row per skill) |
| `worker_location.csv` | worker, latitude, longitude |
| `worker_range.csv` | worker, range (km) |
| `worker_cost.csv` | worker, cost |
| `worker_skills.csv` | worker, skill (one row per skill) |
| `worker_task_history.csv` | worker, task |
| `worker_skills_incomplete.csv` | worker, probability, skill |

IDs are 1-indexed integers. Then run `python experiments/run_all.py`.

### Data Pipeline
```
gen_data.py → populate_csv.py → data/raw/*.csv → gen_input.py → experiments/
  (generate)     (write CSVs)                       (load)        (run)
```
