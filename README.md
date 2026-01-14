# Pac-Man FPGA (DE1-SoC, SystemVerilog)

A grid-based Pac-Man style game implemented on the DE1-SoC FPGA using SystemVerilog. The design uses ROM-based maze data, RAM-based coin tracking, VGA rendering, and finite state machines for movement, collision detection, and scoring.

---

## Features
- Tile-based Pac-Man movement on a 30×36 node grid
- Walkability determined by ROM-backed maze map
- Neighbor scanning FSM to validate legal moves
- Pac-Man style “sticky” movement with direction memory
- Horizontal tunnel teleportation (left ↔ right edge)
- Coin system implemented using dual-port RAM
- Coins removed when player overlaps a node
- Coin counter increments on coin collection
- Score (coins collected) displayed on HEX displays
- VGA rendering of maze, coins, player, and enemies
- NES (N8) controller input with edge detection

---

## Hardware / Tools
- Target board: DE1-SoC (Cyclone V)
- Clock: 50 MHz base clock
- Toolchain: Quartus Prime Lite, ModelSim Intel
- Display: VGA (290×410 active area)
- Inputs: NES (N8) controller via GPIO, reset via KEY
- Memory:
  - ROM (MIF) for maze walkability
  - Dual-port RAM for coin map
- Outputs:
  - VGA for game rendering
  - HEX displays for score output

---

## Repo Structure
```
/
├── src/
│   ├── DE1_SoC.sv
│   ├── player_ctrl.sv
│   ├── node_neighbour_reader.sv
│   ├── coin_counter.sv
│   ├── vga_background.sv
│   ├── video_driver.sv
│   ├── coin_map_ram.v
│   ├── edge_detect.sv
│   └── seg7.sv
├── mif/
│   ├── node_map.mif
│   └── coin_map.mif
├── sim/
│   ├── node_neighbour_reader_tb.sv
│   └── runlab.do
├── tools/
│   ├── map_to_mif.py
│   └── coin_init.py

```
---

## How It Works (short)
- Maze: Hand-drawn grid converted into a walkability MIF file using Python and NumPy.
- Neighbor Reader: FSM scans up, down, left, and right neighbors from ROM before movement.
- Player Controller: Sticky Pac-Man movement with desired direction memory and tunnel logic.
- Coins & Score: Coins stored in RAM; when Pac-Man overlaps a node, the RAM bit is cleared and the score counter increments.
- Display: Score is shown on HEX displays using 7-segment encoding.

---

## Simulation (ModelSim)
```tcl
vlib work
vlog -sv src/*.sv
vsim work.DE1_SoC
run -all
```

Note: ModelSim Intel requires all local variables to be declared before procedural statements.

⸻

Build / Program (Quartus)
	1.	Open Quartus Prime.
	2.	Set the top-level entity to DE1_SoC.
	3.	Add all source files and memory initialization files.
	4.	Compile and program the DE1-SoC.


⸻

Conventions
	•	Node grid: 30 columns × 36 rows
	•	Addressing: addr = row * 30 + column
	•	Maze origin offset: MAP_X0 = 0, MAP_Y0 = 30
	•	Coin RAM output is registered (1-cycle latency)
	•	All FSM outputs are registered
	•	Score increments only on a valid coin removal event

⸻

What I Fixed
	•	Prevented illegal movement into unwalkable nodes
	•	Fixed ROM timing issues by adding explicit wait states
	•	Corrected VGA coin alignment using tile-center math
	•	Eliminated double-draw coin artifacts via pipelining
	•	Ensured coin counter increments exactly once per coin

⸻

To-dos
	•	Add win condition when all coins are collected
	•	Add game-over screen on enemy collision
	•	Add difficulty scaling over time
	•	Add sound effects for coin collection

⸻

License

MIT
