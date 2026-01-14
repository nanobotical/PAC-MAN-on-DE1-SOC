



# Pac-Man FPGA — Portfolio Notes

---

## Libraries / Frameworks
- Quartus Prime Lite
- ModelSim Intel
- Intel FPGA ROM and RAM IP
- Python with NumPy for MIF generation

---

## Blocks
- Block 1 — Player Controller  
  FSM-driven Pac-Man movement with direction memory, tunnel logic, and coin detection.

- Block 2 — Neighbor Reader  
  Multi-state ROM reader that scans adjacent nodes with correct memory timing.

- Block 3 — Coin System & Score Counter  
  Dual-port RAM tracks coin presence; a counter increments on coin collection and drives HEX displays.

- Block 4 — VGA Renderer  
  Tile-based rendering of maze, coins, player, and enemies using pipelined memory access.

---

## Features
- ROM-based walkability map generated from a hand-drawn maze
- RAM-based coin tracking with live modification
- Sticky directional movement similar to classic Pac-Man
- Hardware-accurate handling of ROM and RAM latency
- Real-time score display on 7-segment HEX displays

---

## Issue 1 — ROM read timing bug
- Bug: Player could enter unwalkable nodes or get stuck.
- Root cause: ROM output was registered, but logic assumed combinational reads.
- Fix: Added address → wait → read → latch FSM sequencing.
- Verified: ModelSim waveforms confirmed correct address/data alignment.

---

## Issue 2 — Coin duplication and miscount
- Bug: Coins appeared duplicated and score incremented incorrectly.
- Root cause: Coin RAM output was not pipelined with node coordinates.
- Fix: Registered coin RAM output and generated a single-cycle `coin_collected` pulse.
- Verified: Visual VGA output and score increments matched expected behavior.

---

## Performance
- Metric 1: Stable VGA output at 50 MHz
- Metric 2: Deterministic FSM timing with no combinational memory access
- Memory usage: 1080-bit walkability ROM + 1080-bit coin RAM
- Accuracy: N/A
- Power: N/A (mains-powered FPGA board)

---

## Improvements
- Add multi-digit decimal score display
- Add high-score retention
- Add ghost AI behavior modes
- Add animation frames for player and enemies

---

## Takeaways
- Respecting memory latency is critical in FPGA design.
- FSM-based control simplifies complex timing problems.
- Pipelining is essential for clean VGA rendering.
- Python tooling dramatically reduced debugging time.
