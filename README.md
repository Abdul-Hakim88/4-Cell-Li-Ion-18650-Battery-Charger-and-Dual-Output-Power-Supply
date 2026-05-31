# 4-Cell Li-Ion 18650 Battery Charger and Dual-Output Power Supply

Two-layer PCB designed in Altium Designer. Four independent 18650 charging channels with per-cell protection, an LM2576 buck converter input stage, and two simultaneous output rails — 5V 2A regulated and 3.7–14.8V 500mA switchable series output.

---

## System Architecture

```
Raw DC Input (up to 40V)
        │
   LM2576-5.0WT
   Buck Converter
        │
      5V Rail ──────────────────────────── OUTPUT 5V 2A (J5)
        │
        ├── TP4056 CH1 ── DW01A + FS8205 ── BT1
        ├── TP4056 CH2 ── DW01A + FS8205 ── BT2
        ├── TP4056 CH3 ── DW01A + FS8205 ── BT3
        └── TP4056 CH4 ── DW01A + FS8205 ── BT4
                                              │
                                    DIP Series Switch
                                    + 1N5819 Bypass Diodes
                                              │
                                OUTPUT 3.7–14.8V 500mA (J4)
```

---

## Features

- 4 independent CC/CV charging channels at 500mA per cell
- Per-cell protection: overvoltage, undervoltage, overcurrent via DW01A + FS8205
- LM2576-5.0WT buck converter — accepts raw DC input up to 40V
- Direct 5V bypass input header for bench supply use
- Output rail 1: 5V 2A — regulated, from buck converter
- Output rail 2: 3.7V / 7.4V / 11.1V / 14.8V at 500mA — DIP-switch-controlled series battery output
- Individual channel enable/disable via DIP switch
- 1N5819 Schottky bypass diodes per series node — excluded cells never float
- Per-channel CHRG (red) and STDBY (green) LED indicators
- Voltmeter module on output rail for real-time monitoring

---

## Hardware

### Bill of Materials

| Ref          | Qty | Description                  | Part Number       | Value / Rating                  |
|--------------|-----|------------------------------|-------------------|---------------------------------|
| U1,U3,U5,U7  | 4   | Li-Ion Charger IC            | TP4056            | 500mA, SOP-8                    |
| U2,U4,U6,U8  | 4   | Battery Protection IC        | DW01A             | OVP / UVP / OCP                 |
| Q1–Q4        | 4   | Dual N-Channel MOSFET        | FS8205            | 20V, 6A, SOT-23-6               |
| VR1          | 1   | Buck Converter               | LM2576-5.0WT      | 5V fixed, 3A, TO-263-5          |
| D9           | 1   | Schottky Diode               | 1N5822            | 3A, 40V, freewheeling           |
| D10–D12      | 3   | Schottky Diode               | 1N5819-G          | 1A, 40V, series bypass          |
| L1           | 1   | Inductor                     | —                 | 100µH, 3A                       |
| BT1–BT4      | 4   | 18650 Battery Holder         | Keystone 1042     | SMD, single cell                |
| R1,R6,R11,R16| 4   | Resistor                     | —                 | 100Ω, 0.25W                     |
| R5,R10,R15,R20| 4  | PROG Resistor                | —                 | 2kΩ, 0.25W (500mA charge)       |
| Rx (LED)     | 8   | Resistor                     | —                 | 1kΩ, 0.25W                      |
| C1,C3,C5,C7  | 4   | Ceramic Capacitor            | —                 | 100nF, 50V                      |
| C2,C4,C6,C8  | 4   | Electrolytic Capacitor       | —                 | 10µF, 10V                       |
| C9,C10       | 2   | Electrolytic Capacitor       | —                 | input filter, see schematic     |
| C0           | 1   | Electrolytic Capacitor       | —                 | 100µF, 10V, buck output         |
| OS1 (DIP1)   | 1   | DIP Switch, 4-position       | —                 | channel charge enable           |
| DIP2         | 1   | DIP Switch, 4-position       | —                 | series output control           |
| J4           | 1   | Connector, 2-pin             | —                 | Output 3.7–14.8V 500mA          |
| J5           | 1   | Connector, 2-pin             | —                 | Output 5V 2A                    |
| J2           | 1   | Connector, 2-pin             | —                 | Regulated 5V 2A bypass input    |
| J1           | 1   | DC Barrel Jack or screw term | —                 | Raw DC input                    |
| —            | 1   | Voltmeter Module             |                   | panel mount, output monitoring  |

### GPIO / Net Summary

| Net    | Description                        | Trace Width |
|--------|------------------------------------|-------------|
| +5     | 5V regulated rail                  | 70 mil     |
| Batt1–4| Per-cell charge/discharge path     | 35 mil      |
| GND    | Ground plane (bottom layer)        | plane       |
| Signal | PROG, TEMP, LED drive              | 15 mil      |
| GND stubs | IC GND pins to plane via        | 35 mil      |

---

## PCB Design

- 2-layer board, designed in Altium Designer
- Top layer: components, routing, 120 mil 5V power trace
- Bottom layer: full GND copper plane, thermal relief on through-hole pads
- Thermal vias under every TP4056 exposed pad (4–6 × 0.3mm drill)
- Channels spaced minimum 8–10mm centre-to-centre for thermal headroom
- Three zones: 5V logic/charge, battery output, series switch/output
- Octagonal board outline, chamfered corners

### Trace Width Reference

```
120 mil  → 5V main power rail
 40 mil  → GND stubs to plane
 32 mil  → charge paths, BAT traces, series output
 12 mil  → signal (PROG, TEMP, LED)
```

---

## Output Rail Behaviour

### Rail 1 — 5V 2A
Regulated output from the LM2576 buck converter. Available continuously regardless of battery state.

### Rail 2 — Series Battery Output

| DIP2 Switches Closed | Cells in Series | Nominal Voltage | Max Current |
|----------------------|-----------------|-----------------|-------------|
| 1                    | 1               | 3.7V            | 500mA       |
| 2                    | 2               | 7.4V            | 500mA       |
| 3                    | 3               | 11.1V           | 500mA       |
| 4                    | 4               | 14.8V           | 500mA       |

Output voltage follows cell state of charge. Not regulated. 1N5819 Schottky diodes ensure excluded cells remain in a defined state when their switch is open.

---

## Fabrication

Gerber files are located in `/fabrication/gerbers/`. Generated from Altium Designer, verified against IPC-2221 clearance and creepage rules.

Recommended fab specs:
```
Layers:          2
Board thickness: 1.6mm
Copper weight:   1oz (35µm)
Min trace/space: 6 mil / 6 mil
Min drill:       0.3mm
Surface finish:  HASL or ENIG
Solder mask:     Both sides
Silkscreen:      Top side
```

---

## License

MIT License. See `LICENSE` for full terms.
