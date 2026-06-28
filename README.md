# Wumpus-Logic-Navigator
A browser-based, single-file AI simulation of the classic Wumpus World problem. A Knowledge-Based Agent navigates a partially observable grid using Propositional Logic and Resolution Refutation — proving cells safe or dangerous purely through logical deduction before making any move.

> Built with vanilla HTML, CSS, and JavaScript. No frameworks, no dependencies.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Demo](#demo)
- [How It Works](#how-it-works)
- [Architecture](#architecture)
- [AI Methodology](#ai-methodology)
- [UI & Features](#ui--features)
- [Limitations & Future Work](#limitations--future-work)
- [How to Run](#how-to-run)
- [Author](#author)

---

## Overview

The **Wumpus World** is a canonical problem in Artificial Intelligence that tests an agent's ability to reason under uncertainty. The agent must navigate a grid containing hidden **Pits** and a **Wumpus** — deadly hazards it cannot see directly. It can only sense:

| Percept | Meaning |
|---|---|
| **Breeze** | A pit exists in an adjacent cell |
| **Stench** | The Wumpus is in an adjacent cell |
| *(silence)* | All adjacent cells are safe |

The agent builds a **Knowledge Base (KB)** from these percepts and uses **Resolution Refutation** to logically prove which cells are safe before stepping into them.

---

## Demo

Open `index.html` directly in any modern browser — no server required.

```
Open index.html → Set grid size → Click NEXT MOVE
```

The grid updates live with colour-coded inference results after each step.

---

## How It Works

```
Agent enters a cell
        │
        ▼
┌─────────────────────┐
│  Perceive           │  ← Read BREEZE / STENCH / None
│  (GridWorld)        │
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  Update KB          │  ← Translate percepts into CNF clauses
│  (LogicAgent)       │     e.g. BREEZE → ¬BRZ ∨ PIT_n1 ∨ PIT_n2 ...
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  Query KB           │  ← Run Resolution Refutation on each neighbour
│  (PropositionalKB)  │     Prove ¬PIT and ¬WMP for each candidate cell
└────────┬────────────┘
         │
         ▼
┌─────────────────────────────────────┐
│  Move Decision                      │
│  ├─ Proven safe neighbour → move    │
│  └─ None proven → cautious random  │
└────────┬────────────────────────────┘
         │
         ▼
  Render grid + update inference log
```

---

## Architecture

The codebase is split into four cleanly separated sections:

| Section | Class | Responsibility |
|---|---|---|
| Logic Engine | `PropositionalKB` | Maintains the KB in CNF; runs resolution refutation to prove or disprove literals |
| Environment | `GridWorld` | Generates the hidden grid, spawns hazards probabilistically, computes percepts |
| Agent | `LogicAgent` | Observes percepts, encodes them as clauses, queries KB, and decides moves |
| Presentation | Rendering & UI | DOM rendering, grid layout, live metrics panel, colour-coded inference log |

### File Structure

```
wumpus-logic-navigator/
│
└── index.html      # Entire application — HTML + CSS + JS in one file
```

---

## AI Methodology

### Knowledge Representation — CNF

All facts and rules are stored in **Conjunctive Normal Form**. When the agent senses a percept, it generates clauses automatically.

Example — Breeze sensed at `(r, c)`:

```
Logical rule:   BREEZE(r,c) ⇒ PIT(n1) ∨ PIT(n2) ∨ ...
CNF clause:     ¬BRZ_r_c ∨ PIT_n1 ∨ PIT_n2 ∨ ...
```

If no breeze is sensed, the agent adds a unit clause eliminating pits from every neighbour:

```
¬PIT_n1,  ¬PIT_n2, ...
```

### Resolution Refutation

`proveByRefutation(goalLiteral)` proves a claim by contradiction:

1. Assert the **negation** of the goal into a temporary copy of the KB
2. Repeatedly resolve pairs of clauses, looking for complementary literals
3. If a **contradiction (empty clause ⊥)** is derived → goal is proven true
4. If no new clauses can be generated (fixed point) → goal cannot be proven

```
Prove cell (2,3) is safe:
  Assert: PIT_2_3 (negation of ¬PIT)
  If KB ∪ {PIT_2_3} derives ⊥ → cell is proven pit-free ✔
```

A resolution limit of **3000** steps is enforced to prevent worst-case exponential blowup.

### Cautious Fallback

When the KB cannot prove any neighbour is safe (genuinely ambiguous state), the agent selects a random unvisited neighbour rather than halting — mimicking cautious exploration under uncertainty.

---

## UI & Features

- **Configurable grid size** — 3×3 up to 8×8
- **Step-by-step execution** — advance one move at a time to observe each inference
- **Colour-coded cells** — visual distinction between proven safe, inferred hazard, explored, and unknown
- **Live metrics panel** — total inference resolutions, cells explored, active percepts
- **Inference log** — timestamped log of every percept, deduction, and move with colour-coded severity levels
- **Responsive layout** — scales cell size to viewport width

### Cell Legend

| Colour | Meaning |
|---|---|
| 🟢 Green glow | Proven safe by inference |
| 🔴 Red glow | Inferred to contain a hazard |
| Dark blue | Explored and revealed |
| Near-black | Unknown / unvisited |
| Gold outline | Current agent position 🤖 |

---

## Limitations & Future Work

| Limitation | Proposed Fix |
|---|---|
| Blind clause pairing in resolution | Implement **Unit Resolution** or **Set of Support** heuristic for faster proofs |
| Agent only looks at immediate neighbours | Add **A\* or BFS** pathfinding to allow backtracking to safe unexplored areas |
| Percept-to-CNF logic is hardcoded | Write a parser that converts general logical sentences to CNF automatically |

---

## How to Run

No installation or build step required.

```bash
# Clone the repository
git clone https://github.com/naynay575/wumpus_agent.git
cd wumpus_agent

# Open in browser
open index.html        # macOS
start index.html       # Windows
xdg-open index.html    # Linux
```

Or simply double-click `index.html` in your file explorer.

**Browser support:** any modern browser (Chrome, Firefox, Edge, Safari).

---

## Author

**Nehal Aftab**
Roll No: 24F-0518 · BCS-2E
FAST-NUCES CFD Campus, Faisalabad

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Post-blue)](https://www.linkedin.com/posts/nehal-aftab-0772243a5_github-ai-coding-ugcPost-7456036215201435648-InG0)
