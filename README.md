# Direction-Optimizing Breadth-First Search

**Course:** CSD319 - Design and Analysis of Algorithm  
**Reference Paper:** *Direction-Optimizing Breadth-First Search* by Scott Beamer 

---

## 📌 Project Background
Graph traversal is a fundamental operation in computing, crucial for shortest path algorithms, social network analysis, and data mining. However, modern real-world graphs (like social networks) are massive and exhibit properties like low diameter and scale-free degree distributions. 

Conventional parallel BFS algorithms suffer from poor locality, thread contention, and significant redundant work because millions of edges must be simultaneously searched at every frontier expansion. The primary bottleneck is not computation, but memory-bound edge checking. This inefficiency necessitates smarter, direction-aware parallel algorithms.

## ⚙️ Algorithmic Approach
This repository utilizes a hybrid parallel algorithm that dynamically switches between **top-down** and **bottom-up** traversal based on the frontier size.

* **Top-Down Search:** Used when the frontier is small. A standard parallel approach where threads explore neighboring nodes.
* **Bottom-Up Search:** Used when the frontier size becomes large. Each thread processes unvisited nodes to check if any neighbors are in the frontier. This allows for an "early stop" behavior that drastically reduces edge checking and eliminates contention. 

Two tuning thresholds (`alpha = 14` and `beta = 24`) are used as heuristics to dictate exactly when to switch directions, maintaining algorithmic correctness while achieving significant speedups on real-world graphs.

## 📊 Experimental Implementation & Results
The algorithm was implemented using C++ with **OpenMP** enabled for multithreading. We executed the BFS program on a supplied graph file and recorded execution time across varying thread counts (1, 2, 4, 8, and 16 threads).

### Thread Scaling Results
| Threads | Execution Time (s) | Speedup | Efficiency |
|---------|--------------------|---------|------------|
| 1       | 0.00400            | 1.00x   | 100%       |
| 2       | 0.00300            | 1.33x   | 67%        |
| 4       | 0.00000            | ∞       | -          |
| 8       | 0.00000            | ∞       | -          |
| 16      | 0.00400            | 1.00x   | 6%         |

**Analysis:** Increasing the thread count ultimately reduces efficiency because BFS is inherently a memory-bound and irregular algorithm. As more threads are introduced, memory contention and synchronization overheads increase. By 16 threads, efficiency drops to 6%, demonstrating that beyond a certain limit, adding threads halts progress rather than speeding it up.

## 🚀 Future Directions
Based on our analysis, we identified several gaps and areas for future optimization:
1. **Adaptive Tuning:** Shifting away from fixed parameters to mechanisms that automatically set switching thresholds at runtime based on graph behavior.
2. **Meta-Hybrid Strategies:** Utilizing pre-execution graph structure analysis to alternate between Direction-Optimizing BFS (for low-diameter graphs) and traditional graph partitioning (for high-diameter graphs).
3. **Algorithmic Generalization:** Expanding this "push/pull" direction-switching logic to other graph algorithms, such as Betweenness Centrality.

---
*Note: The core C++ reference code in this repository relies on the GAP Benchmark Suite (gapbs) by Scott Beamer. Please refer to the included `daa report.pdf` for our full team analysis.*