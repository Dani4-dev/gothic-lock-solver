# Gothic 1 Remake — Lock Puzzle Solver

A browser-based solver and visualizer for the lockpicking puzzles in **Gothic 1 Remake**.

**[🌐 Launch Gothic Lock Solver](https://dani4-dev.github.io/gothic-lock-solver/)** — use the app directly in your browser.

Set the number of plates, describe which plates move together, and select their starting pin positions. The application searches for a sequence of moves that places every pin in the center hole, then lets you watch the lock open one step at a time.

**No installation, backend, or internet connection required.** Everything runs locally in a single HTML page.

## Features

- **4–12 plates:** change the number of plates using the `−` / `+` controls or the number field.
- **Configurable connections:** specify which other plates move when a plate is moved, and whether they move in the same or opposite direction.
- **Multiple connections:** use `+ Add` to link a plate to more than one other plate.
- **Live pin positions:** select one of seven holes for each plate. The target is always **position 4**.
- **Shortest-move solutions:** find a sequence of valid moves and see the pin positions after each step.
- **Animated lock:** watch the plates slide over fixed pins, using your own PNG sprites or the built-in fallback graphics.
- **Playback controls:** `Play Solution`, `Play Step`, and `Replay`.
- **Convenient editing:** green highlighting for configured plates, `Reset Connections`, `Reset All`, and a built-in example.
- **Copy Solution:** copy the calculated instructions as text.
- **Automatic local saving:** the browser remembers the current configuration where local storage is available.

## Getting started

1. Download or clone this project.
2. Keep `gothic_lock_solver.html` next to the `assets` folder (see below).
3. Open `gothic_lock_solver.html` in a modern browser, such as Chrome, Firefox, or Edge.
4. Configure your lock and click **Solve Lock**.

You do not need Python, Node.js, a web server, or any external libraries. The page also works without the PNG files, using its built-in graphics.

### Project structure

```text
Gothic-Lock-Solver/
├── gothic_lock_solver.html
├── README.md
└── assets/
    ├── lock.png
    ├── yellow.png
    └── red.png
```

The three optional sprite files are expected to be **175 × 40 pixels** each:

| File | Purpose |
| --- | --- |
| `lock.png` | A plate viewed from above, with seven holes |
| `yellow.png` | A fixed pin that is not in the fourth hole |
| `red.png` | A fixed pin aligned with the fourth hole |

The plates are rendered **above** the pin images. Pins remain in a fixed vertical column while the plates move horizontally by **22 pixels per hole**.

## How to use

### 1. Configure the plates

In **Lock Configuration**, choose the number of plates (4–12). Each plate has connection options:

- **`_`** — the selected plate moves on its own; no other plate is linked by that row.
- **`= Same`** — the linked plate moves in the same direction.
- **`↔ Opposite`** — the linked plate moves in the opposite direction.
- **`+ Add`** — adds another connection row directly under that plate.
- **`×`** — removes an additional connection row.

For example, if moving **Plate 3** also moves **Plate 6** in the same direction and **Plates 4 and 5** in the opposite direction, add three connections to Plate 3:

```text
Plate 3 → Plate 6: Same
        → Plate 4: Opposite
        → Plate 5: Opposite
```

A plate with modified connections is highlighted in light green. **Reset Connections** clears every plate's links, removes additional rows, and restores the defaults (`_` and `= Same`) **without changing the pin positions or plate count**.

### 2. Set the starting pin positions

In **Pin Positions**, select the current hole (**1–7**) for each plate.

- Every pin must end in **hole 4**.
- Plates are listed **bottom to top**: Plate 1 is the bottom row, and the highest-numbered plate is the top row.
- The Lock Visualization updates immediately as you change a pin position.

### 3. Solve the lock

Click **Solve Lock**. The **Solution** panel displays a table such as:

| Step | Action | Pin positions (1–5) |
| --- | --- | --- |
| Start | — | 2–3–4–5–4 |
| 1 | Plate 1 ← ×2 | 4–1–4–5–4 |
| 2 | Plate 2 ← ×1 | 4–2–4–5–5 |
| 3 | Plate 4 → ×2 | 4–4–4–3–5 |
| 4 | Plate 5 → ×1 | **4–4–4–4–4** |

The example assumes these connections: Plate 1 → Plate 2 (Opposite), Plate 2 → Plate 5 (Same), Plate 3 → Plate 2 (Same), Plate 4 → Plate 2 (Opposite), and Plate 5 → Plate 4 (Opposite).

Each step combines consecutive moves of the same plate in the same direction. **`×2` means two individual moves**, not one two-hole jump. Every intermediate move must be valid.

**Important:** the arrows in the solution describe the **physical movement of the plate on screen**, not the direction in which the numbered pin position changes. For example, `Plate 1 ←` moves the plate left, causing its pin to appear in a higher-numbered hole.

### 4. Watch the solution

In **Lock Visualization**:

| Button | Action |
| --- | --- |
| **Play Solution** | Start the complete animation from the original positions |
| **Play Step** | Animate one grouped table step, then stop; click again for the next step |
| **Replay** | Restart the complete animation from the beginning |

The current action appears in the corner of the visualization (for example, `Plate 5 → ×3`). The active row in the solution table is also highlighted.

The animation does **not** modify your selected **Pin Positions**. Editing the configuration invalidates the previous solution so you can solve the updated lock again.

## Other controls

| Control | What it does |
| --- | --- |
| **Example** | Load a sample six-plate lock and calculate its solution |
| **Reset Connections** | Reset only connections and extra connection rows |
| **Reset All** | Reset connections and pin positions, keeping the current plate count |
| **Copy Solution** | Copy the action table to the clipboard |
| **Cancel Search** | Stop a calculation that is still running (when the search is cancellable) |

## Solver behavior and limitations

- Each plate has **seven positions (1–7)**; a move is rejected if any affected plate would leave that range.
- When a plate moves, its directly linked plates move **simultaneously**. There are **no chain reactions**: moving a linked plate does not trigger that plate's own connections.
- Every individual move costs one action. When a solution is found, the algorithm looks for the **minimum number of individual moves** under these rules.
- For **4–6 plates**, the solver uses a reverse breadth-first search and prefers fewer grouped steps among equally short solutions.
- For **7–12 plates**, it uses a chunked bidirectional search and splits disconnected plate groups where possible, so the browser can remain responsive. Search is limited to approximately **450,000 explored states**. Some complex locks may reach this limit without a result; this **does not mean the lock is unsolvable**.
- The solver follows the rules you enter. If the game's actual plate interactions differ, the calculated solution may not match the in-game result.

## Troubleshooting

**My images are not showing.** Verify that the `assets` folder is next to `gothic_lock_solver.html` and that its filenames match exactly: `lock.png`, `yellow.png`, and `red.png`. The application falls back to built-in graphics when images cannot load.

**The solution directions look reversed.** The arrows show **plate movement**, while the numbers represent the hole under the stationary pin. Moving a plate left increases the displayed hole number.

**The solver says “No solution exists.”** Double-check the starting positions and connections. Under the rules provided, the target cannot be reached without an invalid move.

**The solver reaches its search limit.** Larger connected puzzles can require many states. A search-limit message is not proof that no solution exists.

**My settings changed unexpectedly.** The application saves settings in browser-local storage when available. Use **Reset Connections** or **Reset All** to start fresh.

## Technology

Built with **HTML, CSS, and vanilla JavaScript**. No frameworks, package manager, or build step required.

---

*Unofficial fan-made tool for Gothic 1 Remake. Not affiliated with the game's developers or publisher.*
