# AI Logic-Based Minesweeper Solver

This repository contains a Python-based autonomous agent designed to solve the **Minesweeper Game** by leveraging First-Order Logic (FOL) inference and dynamic knowledge base management. The project is implemented using `pyDatalog` to embed a declarative Prolog-like reasoning engine directly within a native Python environment. This project has been developed as a **B.Sc. Course Project for Artificial Intelligence** at the **University of Isfahan (UI)**.

The agent models the game board as an evolving relational universe, systematically deducing completely safe coordinates to reveal and identifying guaranteed mine locations to flag, while employing an advanced probabilistic fallback strategy when faced with logical deadlocks.

---

## Core Logical Components

The system architecture cleanly decouples game state tracking, knowledge representation, and logical deduction into five core phases:

1. **Static Fact Generation (`init_static_facts`)**
   * **Role:** Establishes the immutable spatial framework of the board.
   * **Behavior:** Maps the grid topology at launch by declaring directional adjacency assertions (`Adjacent`) across all coordinate pairs, eliminating the need to re-evaluate grid boundaries during active search loops.

2. **First-Order Logic Rules (`init_rules`)**
   * **Role:** Defines the governing axioms for deductive reasoning based on cell states.
   * **Behavior:** Implements the core inference rules:
     * **Safety Rule:** If an opened cell's revealed value (*V*) matches its surrounding flagged count (*F*), all remaining hidden neighbors are logically deduced as *Safe*.
     * **Danger Rule:** If an opened cell's remaining unrevealed neighbor count (*H*) matches its remaining hidden mine obligation (*V* - *F*), all remaining hidden neighbors are definitively marked as a *Mine*.

3. **Dynamic Knowledge Base Management (`update_knowledge_base`)**
   * **Role:** Keeps the inference engine synchronized with the visual interface.
   * **Behavior:** Optimizes memory usage by actively purging stale facts from previous steps using targeted retraction mechanisms (`pyDatalog.retract_facts`). It translates the agent's current board perception into live logical clauses (`CellIsHidden`, `CellValue`, `RemMines`).

4. **Logical Inference Querying (`query_solver`)**
   * **Role:** Interfaces with the `pyDatalog` execution stack.
   * **Behavior:** Dispatches deterministic queries to resolve the truth values of `Safe(R, C)` and `Mine(R, C)`. It batches the resulting coordinate tuples into clean action pipelines to maximize execution throughput.

5. **Uncertainty Handling Strategy (`get_safest_guess`)**
   * **Role:** A heuristic risk-assessment fallback for zero-certainty scenarios.
   * **Behavior:** When the logical engine encounters a deadlock (no guaranteed safe moves), this function computes the local conditional probability of mine presence across the fringe cells:
     $$Risk = \max \left( \frac{V_{adj} - F_{adj}}{H_{adj}} \right)$$
     It executes a calculated risk minimization step by revealing the cell with the lowest overall boundary threat, falling back to a global background probability check only if isolated.

---

## Evaluation Scenarios

The solver's computational efficiency and deductive accuracy can be tested across four distinct evaluation matrix tiers configured within the `main.py` execution block:

| Scenario Tier | Grid Dimensions | Total Mines | Target Benchmarks & Expectations |
| :--- | :---: | :---: | :--- |
| **1. Simple** | 9 × 9 | 10 | Baseline validation; expected 100% completion rate without heuristic intervention. |
| **2. Standard** | 15 × 15 | 35 | Primary analytical standard; agent must solve the board fully without exploding. |
| **3. Challenging** | 20 × 20 | 75 | High mine density; tests the mathematical resilience of the smart-guessing fallback ($\ge$ 80% progress). |
| **4. Large (Speed)** | 45 × 80 | 200 | Memory leak and stress test; evaluates fact retraction efficiency and real-time execution stability. |

---

## Code Structure & Footprint

To satisfy clean object-oriented architecture and academic software standards, the program workflow is organized sequentially inside `main.py`:

* **FOL Term Setup:** Declares all symbolic logic variables statically to prevent IDE syntax warnings while accommodating `pyDatalog`'s runtime injection.
* **Knowledge Pipeline:** Implements a strict reset-per-tick lifecycle for temporary game assertions to prevent memory accumulation.
* **Action Filter:** Ensures the agent only acts upon high-confidence assertions matching coordinates that are strictly `AGENT_UNKNOWN`.
* **Visual Loop:** Blends logic computation seamlessly with a non-blocking `pygame` render process.

---

## Environment & Requirements

* **Language:** Python 3.12+
* **Core Libraries:**
  * `pyDatalog` (Logic programming engine for Python)
  * `pygame` (GUI game framework and render engine)
  * `random`, `time`, `typing` (Standard Utility Libraries)

---

## Academic Context

* **Institution:** University of Isfahan (دانشگاه اصفهان)
* **Degree:** Bachelor of Science (B.Sc.) in Computer Science
* **Course:** Artificial Intelligence (درس هوش مصنوعی)
* **Professor:** Dr. Faria Nasiri Mofakham

### Group Members
* **Member 1:** Farnoush Pourshaban - [Student ID: 4024023007]
* **Member 2:** Yeganeh Rastegari - [Student ID: 4014013040]
* **Member 3:** Farzaneh Salimi - [Student ID: 4014013059]
