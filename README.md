# Misinformation Diffusion Analysis — Social Network Epidemic Modeling

A big-data pipeline that analyzes how 5G conspiracy misinformation spreads through real social network graphs — using **PySpark + HDFS** for large-scale data processing and a custom **SIR/SEIR epidemic simulator** to model and quantify the spread.

![Python](https://img.shields.io/badge/Python-3.10-blue) ![PySpark](https://img.shields.io/badge/PySpark-3.x-orange) ![NetworkX](https://img.shields.io/badge/NetworkX-graph%20analysis-green) ![License](https://img.shields.io/badge/License-MIT-lightgrey)

---

## What this project does

This project treats the spread of online misinformation like a disease outbreak. Using real social network graph data (users as nodes, follows/interactions as edges, labeled as conspiracy-related or not), it:

1. Cleans and processes millions of nodes and edges at scale using **PySpark on HDFS**
2. Profiles the network structure to detect **bot/seed accounts** that artificially amplify spread
3. Simulates misinformation spread using a custom-built **SIR (Susceptible-Infected-Recovered)** and **SEIR** epidemic model
4. Quantifies *why* misinformation spreads — comparing network topology, the role of bot accounts as "patient zero," and the impact of moderation interventions

This is computational social science: the goal is understanding and counteracting misinformation spread, not amplifying it.

---

## Pipeline overview

```
Raw graph data (HDFS)
   nodes.csv + edges.txt, ~35+ separate social network graphs
        ↓
1. DATA CLEANING (PySpark)
   — schema enforcement, type casting, null handling
   — deduplication of nodes and edges per graph
   — saved as optimized Parquet files
        ↓
2. EXPLORATORY ANALYSIS (PySpark + NetworkX)
   — dominant-label classification per graph (conspiracy vs non-conspiracy)
   — in-degree distribution analysis
   — artificial seed/bot account detection (high graph presence + low real followers)
   — network visualization with conspiracy nodes highlighted
        ↓
3. EPIDEMIC SIMULATION (custom Python class)
   — SIR and SEIR compartmental models run directly on the real graph topology
   — controlled experiments isolating individual variables
        ↓
Quantified findings + executive-style report
```

---

## Key experiments

The simulator isolates three variables independently to answer specific questions:

| Experiment | Question | Method |
|---|---|---|
| **Topology effect** | Does an "echo chamber" network structure spread misinformation faster than a normal network? | Run identical SIR parameters on a conspiracy-dominant graph vs. a non-conspiracy graph |
| **Patient zero effect** | Does it matter *who* starts the spread — a high-degree bot account vs. a random user? | Seed the simulation from the highest out-degree node vs. a randomly chosen node |
| **Intervention efficacy** | How much does removing the top hub accounts reduce overall spread? | Re-run the simulation after removing the top 1% highest-degree accounts |
| **Latency effect** | Does an incubation/exposed period (SEIR) change the outbreak curve vs. instant spread (SIR)? | Compare SIR vs. SEIR dynamics on the same graph |

---

## The epidemic simulator

A custom `EpidemicSimulator` class built on NetworkX, supporting both SIR and SEIR dynamics:

- **States:** Susceptible → (Exposed) → Infected → Recovered
- **Parameters:** `beta` (infection rate), `gamma` (recovery rate), `alpha` (incubation rate, SEIR only)
- Runs directly on the actual graph topology extracted from real data — not a synthetic/random graph
- Supports custom patient-zero seeding, multi-graph batch simulation, and early stopping when the epidemic dies out

---

## Tech stack

PySpark · HDFS · Parquet · NetworkX · Pandas · Matplotlib · Seaborn

---

## Project structure

```
Misinfo-Diffusion-Analysis/
├── notebooks/
│   ├── 01_data_cleaning.ipynb      # PySpark cleaning, dedup, Parquet export
│   ├── 02_eda.ipynb                # graph profiling, bot detection, visualization
│   └── 03_epidemic_simulation.ipynb # SIR/SEIR modeling and experiments
├── requirements.txt
└── README.md
```

---

## Getting started

This project expects data hosted on HDFS via a Spark cluster (e.g. a local Docker Spark + Hadoop setup).

```bash
git clone https://github.com/Kevra-125/Misinfo-Diffusion-Analysis
cd Misinfo-Diffusion-Analysis
pip install -r requirements.txt
```

Run the notebooks in order: data cleaning → EDA → epidemic simulation. Each notebook reads its input from the Parquet output of the previous step.

> **Note:** raw graph data is not included in this repo due to size. The pipeline expects `nodes.csv` / `edges.txt` files structured per graph under HDFS, with conspiracy/non-conspiracy labels embedded in the file path.

---

## Requirements

```
pyspark
pandas
numpy
networkx
matplotlib
seaborn
```

---

## What I learned

- Processing graph-structured data at scale with PySpark, including schema enforcement and deduplication across many separate graphs
- How to detect artificial/bot accounts using simple but effective heuristics (high cross-graph presence + low real engagement)
- Building a compartmental epidemic model (SIR/SEIR) from first principles and applying it outside its usual domain (disease spread → information spread)
- Designing controlled experiments that isolate one variable at a time (topology, seeding, intervention) to produce defensible, individually interpretable conclusions
- Translating technical graph/simulation results into a clear, decision-relevant executive summary

---

## Author

**Mohamed Hany** — Computer Science student at MSA University / University of Greenwich
Cairo, Egypt · [LinkedIn](https://linkedin.com/in/mohamed-hany-a8b9a933b) · [GitHub](https://github.com/Kevra-125)

*Open to freelance ML/data science projects and internships.*
