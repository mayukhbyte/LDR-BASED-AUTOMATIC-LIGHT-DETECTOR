<div align="center">

# 💡 LDR Based Automatic Light Detector

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Electronics](https://img.shields.io/badge/Domain-Analog%20Electronics-blue.svg)]()
[![Platform](https://img.shields.io/badge/Platform-Breadboard%20%7C%20PCB-green.svg)]()
[![Power](https://img.shields.io/badge/Power-9V%20Battery-orange.svg)]()
[![Components](https://img.shields.io/badge/Components-Through--Hole-lightgrey.svg)]()

> **A compact analog circuit that automatically switches LED indicators based on ambient light levels — no microcontroller or programming required.**

---

<img src="https://img.shields.io/badge/BC547-NPN%20Transistor-red?style=for-the-badge" />
<img src="https://img.shields.io/badge/PN2222A-Output%20Stage-purple?style=for-the-badge" />
<img src="https://img.shields.io/badge/LDR-Light%20Sensor-teal?style=for-the-badge" />
<img src="https://img.shields.io/badge/9V-Battery%20Powered-orange?style=for-the-badge" />

</div>

---

## 📋 Table of Contents

- [Abstract](#-abstract)
- [Project Objectives](#-project-objectives)
- [How It Works](#-how-it-works)
- [Circuit Architecture](#-circuit-architecture)
- [Components List](#-components-list)
- [Circuit Diagram (ASCII)](#-circuit-diagram-ascii)
- [Operation Flowchart](#-operation-flowchart)
- [Breadboard Implementation](#-breadboard-implementation)
- [Testing & Observations](#-testing--observations)
- [Applications](#-applications)
- [Advantages & Limitations](#-advantages--limitations)
- [Results](#-results)
- [Conclusion](#-conclusion)
- [References](#-references)
- [License](#-license)

---

## 📝 Abstract

The **LDR Based Automatic Light Detector** is a compact analog electronics project that senses the brightness of the surroundings and switches a set of LED indicators **automatically** — no programming needed.

The core mechanism:

```
Ambient Light ──► LDR Resistance Change ──► Voltage Divider ──► Transistor Switch ──► LED Output
```

| Condition | LDR Resistance | Transistor Q3 | Transistor Q1 | LEDs |
|-----------|---------------|---------------|---------------|------|
| ☀️ Bright Light | Low (~few kΩ) | **ON** | OFF | 🔴 **OFF** |
| 🌑 Darkness | High (~100s kΩ) | OFF | **ON (Saturated)** | 🟡🟢 **ON** |

> The circuit is powered by a **9 V battery** protected by a **1N4007 diode**, and drives **4 LEDs** (2 yellow + 2 green) through a dual-transistor cascade.

---

## 🎯 Project Objectives

| # | Objective | Description |
|---|-----------|-------------|
| 1 | **Understand the LDR** | Study the inverse relationship between illumination and resistance |
| 2 | **Build a light-responsive circuit** | Construct a circuit whose output changes with brightness |
| 3 | **Study transistor switching** | Learn how BJTs work as electronic switches in cascade |
| 4 | **Learn biasing & current limiting** | Understand voltage dividers, base biasing, and LED protection |
| 5 | **Practice circuit protection** | Understand reverse-polarity protection via series diode |
| 6 | **Implement on breadboard** | Assemble and observe behaviour in both bright and dark conditions |
| 7 | **Relate theory to hardware** | Compare simulation schematic with physical wiring |

---

## ⚙️ How It Works

### Physics Behind the LDR

An LDR (Light Dependent Resistor) is made from a **semiconductor material (cadmium sulphide)** whose conductivity increases when photons strike its surface:

```
📡 Photons hit CdS surface
        │
        ▼
   Electron-hole pairs generated
        │
        ▼
   Conductivity ↑ → Resistance ↓
```

| Light Condition | Typical LDR Resistance |
|-----------------|----------------------|
| Bright Sunlight | 100 Ω – 1 kΩ |
| Indoor Light | 1 kΩ – 10 kΩ |
| Twilight | 10 kΩ – 100 kΩ |
| Complete Darkness | 100 kΩ – several MΩ |

### Voltage Divider Principle

The LDR and R1 (4.7 kΩ) form a **potential divider** across the 8.3 V rail:

$$V_b = V_{rail} \times \frac{R_1}{R_1 + R_{LDR}}$$

- **Bright light** → R_LDR low → V_b rises → **Q3 turns ON**
- **Darkness** → R_LDR high → V_b falls → **Q3 turns OFF**

### Cascaded Transistor Logic

```
  LDR Sensor Signal
        │
        ▼
  ┌─────────────┐    inverted    ┌─────────────┐
  │  Q3 (BC547) │ ─────────────► │ Q1 (PN2222A)│ ──► 4 LEDs
  │  Control    │                │  Output     │
  └─────────────┘                └─────────────┘
       Stage 1                       Stage 2
```

**Switching Threshold:** Q3 begins to conduct when LDR resistance drops below **~55 kΩ**, setting the light/dark boundary.

**LED Current Calculation (darkness, LEDs ON):**
```
I_LED ≈ (8.3V − 4V − 0.2V) / 330Ω ≈ 12 mA per branch
```

**Q1 Base Current (darkness):**
```
I_base ≈ (8.3V − 0.7V) / 4.7kΩ ≈ 1.6 mA  →  Q1 saturated
```

---

## 🏗️ Circuit Architecture

The schematic divides into **five functional blocks**:

```
┌────────────────────────────────────────────────────────────────────────┐
│                    LDR AUTOMATIC LIGHT DETECTOR                        │
│                                                                        │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐              │
│  │  POWER BLOCK │   │ SENSING BLOCK│   │ CONTROL BLOCK│              │
│  │              │   │              │   │              │              │
│  │  B1 (9V)     │   │    LDR1      │   │  Q3 (BC547)  │              │
│  │  + D3        │   │    + R1      │   │  Collector   │              │
│  │  (1N4007)    │   │  (Divider)   │   │  → R2 → Q1   │              │
│  │  = 8.3V Rail │   │              │   │  Base        │              │
│  └──────┬───────┘   └──────┬───────┘   └──────┬───────┘              │
│         │                  │                   │                      │
│         └──────────────────┴───────────────────┘                      │
│                                                                        │
│  ┌──────────────────────────────────────────────────────────┐         │
│  │                    OUTPUT BLOCK                          │         │
│  │                                                          │         │
│  │   Q1 (PN2222A) collector ──► Branch 1: R3 + D1 + D5 (🟡)│         │
│  │                          └─► Branch 2: R4 + D2 + D4 (🟢)│         │
│  └──────────────────────────────────────────────────────────┘         │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 🔩 Components List

| Ref | Component | Value/Part No. | Qty | Function |
|-----|-----------|---------------|-----|----------|
| **B1** | Battery | 9 V | 1 | DC supply for the entire circuit |
| **D3** | Diode | 1N4007 | 1 | Reverse-polarity protection |
| **LDR1** | Photoresistor | GL5516 or equiv. | 1 | Light sensor — resistance ∝ 1/illumination |
| **R1** | Resistor | 4.7 kΩ | 1 | Lower arm of LDR potential divider |
| **R2** | Resistor | 4.7 kΩ | 1 | Pull-up / base current supply for Q1 |
| **R3, R4** | Resistor | 330 Ω | 2 | Current limiting for each LED branch |
| **Q3** | NPN Transistor | BC547 | 1 | Sensing / control stage (inverts sensor signal) |
| **Q1** | NPN Transistor | PN2222A | 1 | Output switching stage (drives LEDs) |
| **D1, D5** | LED | Yellow 5 mm | 2 | Yellow indicator branch |
| **D2, D4** | LED | Green 5 mm | 2 | Green indicator branch |
| ─ | Breadboard | Solderless | 1 | Prototype assembly platform |
| ─ | Jumper Wires | Various | ─ | Connections |
| ─ | Battery Clip | 9 V snap | 1 | Power connector |

> **Total estimated cost:** < ₹100 / ~\$1.20 USD using standard components.

---

## 📐 Circuit Diagram (ASCII)

```
        +8.3V (after D3)
           │
     ┌─────┤
     │     │
   [LDR1]  │
     │     │  R2
     ├─────┼──[4.7kΩ]──┐
     │     │            │
  [R1]     │       B ───┤ Q1 (PN2222A)
  4.7kΩ   ─            │ C ──┬── R3[330Ω]──D1(Y)──D5(Y)──┐
     │     │            │      │                            │
     │   B─┤Q3(BC547)   │      └── R4[330Ω]──D2(G)──D4(G)──┘
     │   C─┘            │                                   │
     │   E              │                                   │
     │   │              E                                   │
    GND  GND           GND                                 GND

   B1(9V)──[D3 1N4007]──► +8.3V Rail
        └─────────────────────────────► GND
```

---

## 🔄 Operation Flowchart

```
                    ┌─────────────┐
                    │    START    │
                    └──────┬──────┘
                           │
                           ▼
              ┌────────────────────────┐
              │  Light falls on LDR1   │
              │  (continuously)        │
              └────────────┬───────────┘
                           │
                           ▼
              ┌────────────────────────┐
              │  LDR resistance changes│
              │  Bright → Low          │
              │  Dark   → High         │
              └────────────┬───────────┘
                           │
                           ▼
              ┌────────────────────────┐
              │ Divider (LDR1 + R1)    │
              │ sets voltage Vb at     │
              │ base of Q3 (BC547)     │
              └────────────┬───────────┘
                           │
                           ▼
                  ┌────────────────┐
                  │  Vb > 0.65 V?  │
                  │ (LDR < ~55 kΩ) │
                  └───┬────────────┘
                 YES  │        │  NO
                      ▼        ▼
            ┌──────────────┐  ┌──────────────────┐
            │  Q3 turns ON │  │  Q3 turns OFF     │
            │  Collector   │  │  R2 feeds base    │
            │  → near 0V   │  │  current to Q1    │
            └──────┬───────┘  └────────┬──────────┘
                   │                   │
                   ▼                   ▼
         ┌─────────────────┐  ┌─────────────────────┐
         │ Q1 base drive   │  │  Q1 saturates        │
         │ removed → Q1 OFF│  │  (~1.6 mA base)      │
         └────────┬────────┘  └──────────┬────────────┘
                  │                      │
                  ▼                      ▼
         ┌─────────────────┐  ┌──────────────────────┐
         │   LEDs OFF  🔴  │  │  ~12 mA per branch   │
         │                 │  │  LEDs ON  🟡🟢        │
         └────────┬────────┘  └──────────┬────────────┘
                  │                      │
                  └──────────┬───────────┘
                             │
                             ▼
              ┌─────────────────────────┐
              │  Continue monitoring    │◄─────┐
              │  light level (loop)     │      │
              └────────────────────────-┘      │
                             │                 │
                             └─────────────────┘
```

---

## 🔧 Breadboard Implementation

### Construction Procedure

Follow these steps in order to build the circuit safely:

```
Step 1 ──► Keep battery DISCONNECTED while placing components
Step 2 ──► Mark the +8.3V and GND rails on the breadboard
Step 3 ──► Place LDR1 and R1 (4.7 kΩ) sensing divider
Step 4 ──► Insert BC547 (Q3) — check pinout: EBC or CBE from datasheet
Step 5 ──► Insert PN2222A (Q1) — pinout differs from BC547!
Step 6 ──► Place 1N4007 diode D3 — band = cathode (towards +rail)
Step 7 ──► Fit 330 Ω resistors (R3, R4) and LED branches with correct polarity
Step 8 ──► Complete wiring with jumper wires; check for shorts
Step 9 ──► Connect battery LAST, after verifying all polarities
```

> ⚠️ **Critical:** BC547 and PN2222A have **different pin orders**. Always verify from datasheets before inserting.

### Pin Reference

| Transistor | Pin Order (flat side facing you) |
|------------|----------------------------------|
| BC547 | EBC (Emitter – Base – Collector) |
| PN2222A | EBC (Emitter – Base – Collector) |

---

## 🧪 Testing & Observations

Test under controlled light changes and record results:

| Light Condition | Expected LED State | Your Observation |
|-----------------|-------------------|-----------------|
| ☀️ Bright light / torch on LDR | **LEDs OFF** | _____________ |
| 💡 Normal room light | **LEDs OFF** (or dim near threshold) | _____________ |
| 🌥️ LDR partly shaded | **LEDs begin to glow** | _____________ |
| 🌑 LDR fully covered / dark room | **LEDs fully ON** (🟡🟢) | _____________ |

### Troubleshooting Guide

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| LEDs never turn on | Q1 not saturating | Check R2 connection; verify Q1 pinout |
| LEDs never turn off | Q3 not conducting | Check LDR divider; measure Vb |
| LEDs flicker constantly | Stray LED light hitting LDR | Orient LDR away from LEDs |
| Circuit dead (no response) | Reverse battery / blown component | Check D3 polarity; measure supply |
| Wrong threshold | Component tolerance variation | Replace R1 with a preset potentiometer |

> 💡 **Pro Tip:** Use a multimeter to measure voltage at the **base of Q3** to diagnose sensing vs. output stage faults.

---

## 🌍 Applications

```
┌────────────────────────────────────────────────────────────┐
│               REAL-WORLD APPLICATIONS                       │
├─────────────────────────┬──────────────────────────────────┤
│  🌙 Automatic Night     │  Lamp turns ON at dusk, OFF at   │
│     Lamps               │  dawn — corridors, staircases    │
├─────────────────────────┼──────────────────────────────────┤
│  🚦 Street-Light        │  Small-scale model of automated  │
│     Demonstration       │  street light controller         │
├─────────────────────────┼──────────────────────────────────┤
│  🚨 Light-Sensitive     │  Detects drawer/cabinet opening  │
│     Alarms              │  via sudden light change         │
├─────────────────────────┼──────────────────────────────────┤
│  📱 Display Brightness  │  Concept behind ambient sensors  │
│     Control             │  in phones and monitors          │
├─────────────────────────┼──────────────────────────────────┤
│  🔌 Simple Automation   │  Replace LEDs with a relay to    │
│                         │  switch any low-power AC load    │
├─────────────────────────┼──────────────────────────────────┤
│  🎓 Educational Use     │  Classroom demo of sensors,      │
│                         │  voltage dividers & transistors  │
└─────────────────────────┴──────────────────────────────────┘
```

---

## ⚖️ Advantages & Limitations

### ✅ Advantages

- 🧩 **Simple circuit** — uses only common, inexpensive through-hole components
- 💰 **Low cost** — total BOM under ₹100 / \$1.20 USD
- ⚡ **No programming needed** — pure analog operation
- 📚 **Educational value** — clearly demonstrates sensor → signal → output
- 🛠️ **Easy to modify** — breadboard-friendly, swap components freely
- 🔋 **Low power** — only ~25 mA with all LEDs lit

### ❌ Limitations

- 🎚️ **Variable threshold** — switching point depends on LDR model and tolerances
- 🔁 **No hysteresis** — LEDs may flicker near the light/dark boundary
- 🐢 **Slow response** — LDR responds slower than a photodiode
- 📍 **Placement-sensitive** — LED light can interfere with LDR reading
- 🔩 **Breadboard fragility** — less reliable than a soldered PCB
- 🪫 **Battery variance** — voltage drops as battery drains, shifting the threshold

### 🔮 Suggested Improvements

| Improvement | Benefit |
|-------------|---------|
| Replace R1 with a **preset potentiometer** | Adjustable sensitivity |
| Add a **Schmitt-trigger / comparator** (e.g., LM393) | Sharp switching, eliminates flicker |
| Use a **MOSFET or relay** output | Switch larger AC/DC loads |
| Transfer to a **soldered PCB** | Mechanical robustness |
| Add a **capacitor** across supply | Filter supply noise |

---

## 📊 Results

| Parameter | Value |
|-----------|-------|
| **Supply Voltage** | 9 V (8.3 V after D3 drop) |
| **Switching Threshold** | LDR ~55 kΩ (Q3 base Vb ≈ 0.65 V) |
| **Q1 Base Current (dark)** | ~1.6 mA |
| **LED Branch Current (dark)** | ~12 mA per branch |
| **Total LED Current** | ~25 mA (4 LEDs) |
| **LEDs in Bright Light** | OFF |
| **LEDs in Darkness** | ON (Yellow + Green) |

> The circuit confirms that ambient light changes can be reliably converted into an electrical switching action through a simple two-transistor cascade — without any digital logic.

---

## 🏁 Conclusion

The **LDR Based Automatic Light Detector** successfully demonstrates:

1. **Photoresistive sensing** — how LDR resistance changes with light
2. **Potential divider** — converting resistance change to voltage change
3. **Transistor cascade** — using BJTs as electronic switches
4. **LED current limiting** — protecting output components

The project forms a strong foundation for understanding **automatic lighting systems**, **sensor-based circuits**, and **analog electronics design**.

Future enhancements can include adding a **comparator IC** for hysteresis-free switching, a **relay** for real-world load switching, or migrating to a **PCB** for a finished product.

---

## 📚 References

1. Circuit schematic and breadboard prototype prepared for this project
2. Datasheets — BC547, PN2222A, 1N4007, GL5516 LDR
3. Basic electronics laboratory notes on resistors, LEDs, voltage dividers and transistor switching
4. Boylestad, R. & Nashelsky, L. — *Electronic Devices and Circuit Theory*, Pearson
5. Floyd, T. L. — *Electronic Devices*, Pearson

---

## 📄 License

This project is released under the **MIT License** — free to use, modify, and distribute for educational and personal projects.

```
MIT License — Copyright (c) 2024 mayukhbyte
```

---

<div align="center">

**Made with ❤️ for analog electronics enthusiasts**

[![GitHub](https://img.shields.io/badge/GitHub-mayukhbyte-black?logo=github)](https://github.com/mayukhbyte)

*If this project helped you, consider giving it a ⭐ on GitHub!*

</div>
