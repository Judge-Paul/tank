# Smart Water Tank Monitoring & Control System

IFT 211 — Digital Logic Design, Mid-Semester Lab Assessment (individual submission).

A pure digital-logic water-tank controller built in **Logisim Evolution 5.0.0**:
four level sensors are encoded to a 2-bit level, shown on a seven-segment display; a pump
runs below full and stops at full; and an alarm fires if the tank stays full for three
clock cycles.

> Individual subcircuits are kept **embedded** in the one project file rather than
> duplicated as separate `.circ` files, per the assignment's guidance that a single valid
> project with all subcircuits embedded is acceptable when standalone export would
> introduce duplication. Each subcircuit is still a distinct, editable circuit inside the
> project.

## Circuits (all inside `WaterTankSystem.circ`)

| Circuit               | Role                                              |
| --------------------- | ------------------------------------------------- |
| `MainSystem`          | top-level integration (set as the main circuit)   |
| `SensorEncoder`       | priority encoder: highest active sensor → `L1 L0` |
| `FullComparator`      | `FULL = L1·L0`                                    |
| `PumpController`      | `PUMP = FULL'`                                    |
| `LevelRegister`       | two D flip-flops, stores the level                |
| `OverflowCounter`     | 2-bit saturating counter → `ALARM`                |
| `SevenSegmentDecoder` | level → segments `a`–`g`                          |

> For visibility, **every subsystem is drawn inline in `MainSystem`** as actual gates and D
> flip-flops — no subcircuit boxes:
>
> - Encoder (priority): `N1`, `A1`, `O1`, `O2`
> - Full comparator: `A2`
> - Pump controller: `N2`
> - Level register: `R1`, `R0` (D flip-flops)
> - Overflow counter: `C1`, `C0` (D flip-flops) + `O3`, `O4`, `A3`, `A4`, `A5`
> - 7-segment decoder: `N3`, `N4`, `N5`, `O5`, `O6`, `A6` + a constant (`b = 1`)
>
> The `SensorEncoder`, `FullComparator`, `PumpController`, `LevelRegister`,
> `OverflowCounter`, and `SevenSegmentDecoder` circuits remain in the project as clean
> standalone reference versions.

### Schematic layout

`MainSystem` is arranged in three clearly separated horizontal bands, each a functional
block with its inputs on one side and outputs flowing out the other:

1. **Top band — signal path:** `S0–S3` → Encoder (`N1`,`A1`,`O1`,`O2`) → `L1`/`L0` →
   7-segment Decoder (`N3`,`N4`,`N5`,`O5`,`O6`,`A6`) → Display.
2. **Middle band — full/pump:** Full comparator `A2` (`L1·L0`) → Pump `N2` → PUMP LED.
3. **Bottom band — sequential:** Level register (`R1`,`R0`) and overflow counter
   (`O3`,`A3`,`O4`,`A4`,`A5`,`C1`,`C0`) → ALARM LED, fed by `CLOCK`/`RESET`.

Shared signals travel as Logisim **tunnels** (named net labels) instead of long wires:
`L1`, `L0`, `FULL`, `CLK`, `RST`, `CQ1`, `CQ0`, `CQ0N`. Each band taps a tunnel with a
short stub, so no wire leaves its own block and the three sections read independently.
The two decoder inputs that need `L1'` are inverted **locally**: `N4` drives `O6.in1` and
`N5` drives `A6.in1`, each fed from an `L1` tap — no negated net label is needed. The
7-segment fan-in is tunnelled too and named by segment (`a`, `b`, `c`, `e`, `f`, and `L1`
for `g`; `d = a`), so each display pin has a short labelled lead instead of a wire
wrapping around the display. Inside each block the gates are wired
left→right and the pump and alarm outputs run as straight horizontal lines.

## How to open

1. Open **Logisim Evolution 5.0.0**.
2. `File → Open…` and select `circuits/WaterTankSystem.circ`.
3. The project opens on `MainSystem`.
