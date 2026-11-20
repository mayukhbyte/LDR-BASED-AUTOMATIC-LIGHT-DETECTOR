<div align="center">

# 💡 Automatic Light Detection & Switching Circuit
### Dual-Stage BJT Cascade Architecture with Photoresistive Threshold Sensing

[![MIT License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Implementation-Hardware%20Breadboard-brightgreen.svg)]()
[![Simulation](https://img.shields.io/badge/Simulation-Proteus%20Design%20Suite-blue.svg)]()
[![Supply](https://img.shields.io/badge/Supply-9V%20DC%20Nominal-orange.svg)]()
[![Academic Session](https://img.shields.io/badge/Session-2026--2027-purple.svg)]()

A solid-state, discrete analog control system designed to continuously monitor ambient luminous intensity and autonomously drive multi-branch LED arrays without requiring microcontrollers, programmable firmware, or external clock generation.

---

[Key Highlights](#-key-highlights) •
[Circuit Schematic](#-schematic-diagram) •
[Transistor Cascade Theory](#-mathematical-derivation--transistor-cascade-analysis) •
[Operation Flowchart](#-system-logic-flowchart) •
[Hardware Build](#-hardware-assembly--real-world-prototyping) •
[Bench Observations](#-experimental-observations) •
[Authors](#-authors--academic-credits)

---

</div>

## 📌 Key Highlights

- **Pure Autonomous Analog Switching:** Eliminates quiescent microcontroller current, firmware bugs, and boot-up latency.
- **Cascaded Dual-BJT Inverter:** Employs a low-noise, high-gain preamplifier stage (**BC547**) cascaded into a high-current saturation driver (**PN2222A**).
- **Reverse Polarity Safeguard:** Integrated **1N4007** inline blocking diode guards semiconductor junctions against reverse battery installation.
- **Split-Branch Indicator Array:** Symmetrical parallel branches featuring current-limiting ballast resistors ($330\,\Omega$) governing dual series yellow and green LEDs.

---

## 📐 Schematic Diagram

The complete schematic was designed and validated in **Proteus Design Suite**:

<div align="center">
  <img src="assets/circuit_schematic.jpg" alt="Proteus Circuit Schematic" width="850"/>
  <p><em>Figure 1: Complete circuit schematic illustrating sensing divider, inverter stage Q3, switching driver Q1, and parallel load arrays.</em></p>
</div>

### Sub-Circuit Breakdown

1. **Power Conditioning Block:** 
   - $B_1$ ($9\,\text{V}$) connected via $D_3$ ($1\text{N}4007$).
   - Forward barrier potential reduction: $V_{\text{rail}} = V_{\text{batt}} - V_D \approx 9.0\,\text{V} - 0.7\,\text{V} = 8.3\,\text{V}$.
2. **Voltage Divider Sensing Network:**
   - Upper branch: Cadmium Sulfide ($CdS$) Light Dependent Resistor ($\text{LDR}_1$).
   - Lower branch: Fixed precision pull-down resistor $R_1$ ($4.7\,\text{k}\Omega$).
3. **Primary Sensing & Signal Inverter ($Q_3$):**
   - Transistor: **BC547** NPN.
   - Converts analog divider voltage $V_{B3}$ into collector-to-emitter pull-down logic.
4. **Intermediate Pull-Up Driver ($R_2$):**
   - $R_2$ ($4.7\,\text{k}\Omega$) establishes base drive for $Q_1$ when $Q_3$ is non-conductive.
5. **High-Current Output Stage ($Q_1$):**
   - Transistor: **PN2222A** NPN.
   - Collector sinks load current across parallel LED strings.
6. **Optical Load Array:**
   - Branch 1: $R_3$ ($330\,\Omega$) in series with Yellow LEDs $D_1, D_5$.
   - Branch 2: $R_4$ ($330\,\Omega$) in series with Green LEDs $D_2, D_4$.

---

## 🧮 Mathematical Derivation & Transistor Cascade Analysis

The circuit operates via a non-linear two-stage inverter cascade governed by BJT operating regions: **Cutoff**, **Active**, and **Saturation**.

```mermaid
graph TD
    RAIL["⚡ Rail +8.3V\n9V − 0.7V diode drop"]

    RAIL --> LDR1["🔆 LDR1\nCadmium Sulfide Photoresistor\nR_LDR: 500Ω → 1MΩ"]
    RAIL --> R2["R2 — 4.7 kΩ\nPull-up / Q1 Base Driver"]

    LDR1 --> DIVNODE["VB3 — Divider Node\nVB3 = Vrail × R1 ÷ (R1 + R_LDR)"]
    R1["R1 — 4.7 kΩ\nDivider Lower Arm"] --> DIVNODE

    DIVNODE --> Q3["Q3 — BC547 NPN\nSensing Inverter Stage\nTO-92 Package"]
    R2 --> Q1BASE["Q1 Base Drive Node\nVC3 → IB1 when Q3 OFF"]
    Q3 -->|"Collector pulls VC3 low when ON"| Q1BASE

    Q1BASE --> Q1["Q1 — PN2222A NPN\nHigh-Current Output Driver\nIC_max = 600 mA"]

    Q1 -->|"Collector sinks load current"| BRANCH1["🟡 Branch 1\nR3 (330Ω) + D1 + D5\nYellow LEDs — ~12.5 mA"]
    Q1 --> BRANCH2["🟢 Branch 2\nR4 (330Ω) + D2 + D4\nGreen LEDs — ~11.9 mA"]

    GND(["GND"])
    Q3 --> GND
    Q1 --> GND
    R1 --> GND
```

### 1. Light Sensing Divider Network

The voltage presented to the base of $Q_3$ ($V_{B3}$) is determined by:

$$V_{B3} = V_{\text{rail}} \cdot \left( \frac{R_1}{R_1 + R_{\text{LDR}}} \right)$$

Under nominal room ambient conditions ($V_{\text{rail}} \approx 8.3\,\text{V}$, $R_1 = 4.7\,\text{k}\Omega$):

$$\text{Switching Threshold: } V_{B3} \ge V_{BE(\text{on})} \approx 0.65\,\text{V}$$

Solving for the critical transition resistance $R_{\text{LDR(th)}}$:

$$R_{\text{LDR(th)}} = R_1 \cdot \left( \frac{V_{\text{rail}}}{V_{B3}} - 1 \right) = 4.7\,\text{k}\Omega \cdot \left( \frac{8.3\,\text{V}}{0.65\,\text{V}} - 1 \right) \approx 55.31\,\text{k}\Omega$$

- **Illuminated State ($R_{\text{LDR}} < 55.3\,\text{k}\Omega$):** $V_{B3} > 0.65\,\text{V}$
- **Obscured State ($R_{\text{LDR}} > 55.3\,\text{k}\Omega$):** $V_{B3} < 0.60\,\text{V}$

---

### 2. State Analysis: Daylight / Illuminated State

```mermaid
flowchart TD
    classDef sunNode fill:#78350f,stroke:#f59e0b,stroke-width:2px,color:#fef3c7
    classDef satNode fill:#7f1d1d,stroke:#ef4444,stroke-width:2px,color:#fecaca
    classDef offNode fill:#1e1b4b,stroke:#818cf8,stroke-width:2px,color:#e0e7ff

    A(["☀️ LDR Illuminated\nR_LDR ~ 1 kΩ – 10 kΩ"]):::sunNode
    B["VB3 rises to 2.6V – 6.8V\n≫ threshold of 0.65V"]:::satNode
    C["Q3 (BC547) → Deep Saturation\nVCE3(sat) ≈ 0.15V"]:::satNode
    D["Q1 Base clamped at 0.15V\n< VBE cut-in (0.55V)"]:::satNode
    E["Q1 (PN2222A) → Hard Cutoff\nIB1 = 0 · IC1 ≈ 0 mA"]:::satNode
    F(["🔴 LEDs OFF\nLoad branches open-circuit"]):::offNode

    A --> B --> C --> D --> E --> F
```

| Parameter | Value |
|:----------|:-----:|
| $Q_3$ Base Voltage ($V_{B3}$) | $2.6\,\text{V}$ – $6.8\,\text{V}$ |
| $Q_3$ Operating Region | **Deep Saturation** |
| $V_{CE3(\text{sat})}$ | $\approx 0.15\,\text{V}$ |
| $Q_1$ Operating Region | **Hard Cutoff** |
| Collector Current $I_{C3}$ | $\approx 1.73\,\text{mA}$ |
| Control Stage Power | $P_{Q3} \approx 0.26\,\text{mW}$ (negligible) |
| LED Load Current | **0 mA** |

- In this mode, collector current through $Q_3$ is:
  
  $$I_{C3} \approx \frac{V_{\text{rail}} - V_{CE3(\text{sat})}}{R_2} = \frac{8.3\,\text{V} - 0.15\,\text{V}}{4.7\,\text{k}\Omega} \approx 1.73\,\text{mA}$$

- Power consumed by the control stage is negligible ($P_{Q3} = V_{CE3(\text{sat})} \cdot I_{C3} \approx 0.26\,\text{mW}$).

---

### 3. State Analysis: Darkness / Sensor Obscured

```mermaid
flowchart TD
    classDef darkNode fill:#134e4a,stroke:#2dd4bf,stroke-width:2px,color:#ccfbf1
    classDef satNode fill:#14532d,stroke:#22c55e,stroke-width:2px,color:#bbf7d0
    classDef ledNode fill:#1e3a5f,stroke:#3b82f6,stroke-width:2px,color:#bfdbfe

    A(["🌑 LDR Obscured / Dark\nR_LDR > 100 kΩ – 1 MΩ"]):::darkNode
    B["VB3 drops below 0.3V\n≪ threshold of 0.60V"]:::darkNode
    C["Q3 (BC547) → Cutoff Region\nIC3 ≈ 0 mA"]:::darkNode
    D["R2 injects base drive into Q1\nIB1 = (8.3V − 0.75V) ÷ 4.7kΩ ≈ 1.61 mA"]:::satNode
    E["Q1 (PN2222A) → Deep Saturation\nVCE1(sat) ≈ 0.18V · βforced ≈ 15.1"]:::satNode
    F(["🟡🟢 LEDs ON at Full Brightness\nIC1(total) ≈ 24.4 mA — Both Branches Active"]):::ledNode

    A --> B --> C --> D --> E --> F
```

| Parameter | Value |
|:----------|:-----:|
| $Q_3$ Base Voltage ($V_{B3}$) | $< 0.30\,\text{V}$ |
| $Q_3$ Operating Region | **Cutoff** |
| $Q_1$ Base Current ($I_{B1}$) | $\approx 1.61\,\text{mA}$ |
| $Q_1$ Operating Region | **Deep Saturation** |
| $V_{CE1(\text{sat})}$ | $\approx 0.18\,\text{V}$ |
| Yellow Branch Current | $\approx 12.48\,\text{mA}$ |
| Green Branch Current | $\approx 11.88\,\text{mA}$ |
| Total LED Load Current | $\approx 24.36\,\text{mA}$ |
| Forced Beta ($\beta_{\text{forced}}$) | $\approx 15.1$ — overdrive factor $> 6.5\times$ |

#### Verification of Saturation Overdrive for Output Transistor $Q_1$

**Yellow LED Branch** — two series LEDs, $V_{FY} \approx 2.0\,\text{V}$ each:

$$I_{\text{branch1}} = \frac{V_{\text{rail}} - 2V_{FY} - V_{CE1(\text{sat})}}{R_3} = \frac{8.3 - 4.0 - 0.18}{330} \approx 12.48\,\text{mA}$$

**Green LED Branch** — two series LEDs, $V_{FG} \approx 2.1\,\text{V}$ each:

$$I_{\text{branch2}} = \frac{V_{\text{rail}} - 2V_{FG} - V_{CE1(\text{sat})}}{R_4} = \frac{8.3 - 4.2 - 0.18}{330} \approx 11.88\,\text{mA}$$

**Total Collector Sink Current:**

$$I_{C1(\text{total})} = I_{\text{branch1}} + I_{\text{branch2}} = 12.48 + 11.88 \approx 24.36\,\text{mA}$$

**Forced Beta (Overdrive Verification):**

$$\beta_{\text{forced}} = \frac{I_{C1(\text{total})}}{I_{B1}} = \frac{24.36\,\text{mA}}{1.61\,\text{mA}} \approx 15.13$$

> [!TIP]
> Since $h_{FE(\min)}$ of PN2222A exceeds **100**, the forced $\beta \approx 15.1$ represents an **overdrive factor > 6.5×** — guaranteeing $Q_1$ remains deep in saturation with $V_{CE1(\text{sat})} \approx 0.18\,\text{V}$ and minimal thermal dissipation ($P_{D1} \approx 4.4\,\text{mW}$).

---

## 🔀 System Logic Flowchart

```mermaid
flowchart TD
    classDef startEnd fill:#1e293b,stroke:#3b82f6,stroke-width:2px,color:#f8fafc;
    classDef process fill:#0f172a,stroke:#64748b,stroke-width:1.5px,color:#e2e8f0;
    classDef decision fill:#312e81,stroke:#6366f1,stroke-width:2px,color:#ffffff;
    classDef dayBranch fill:#7f1d1d,stroke:#ef4444,stroke-width:1.5px,color:#fecaca;
    classDef nightBranch fill:#14532d,stroke:#22c55e,stroke-width:1.5px,color:#bbf7d0;

    Start(["System Power Applied (9V Rail Protected via 1N4007)"]):::startEnd --> Sense["Continuous Ambient Lux Incident on LDR1"]:::process
    Sense --> Divide["Voltage Divider (LDR1 + R1) Derives Base Node Voltage VB3"]:::process
    Divide --> Check{"Is VB3 &ge; 0.65V?<br/>(RLDR &le; 55.3 k&Omega;)"}:::decision

    %% Daytime Branch
    Check -- "YES (Bright / Daylight)" --> Q3_ON["Q3 (BC547) Enters Saturation<br/>Collector pulled to VCE3(sat) &approx; 0.15V"]:::dayBranch
    Q3_ON --> Q1_OFF["Base Voltage of Q1 Clamped Low<br/>Q1 (PN2222A) Forced into Cutoff"]:::dayBranch
    Q1_OFF --> LED_OFF["Load Circuits Isolated<br/>IC1 &approx; 0 mA &bull; LEDs Extinguished"]:::dayBranch
    LED_OFF --> LoopBack["Feedback Monitoring"]:::process

    %% Nighttime Branch
    Check -- "NO (Dark / Ambient Dim)" --> Q3_OFF["Q3 Base Starved (VB3 &lt; 0.6V)<br/>Q3 Enters Cutoff Region"]:::nightBranch
    Q3_OFF --> Q1_ON["R2 Injects IB1 &approx; 1.6 mA into Base<br/>Q1 Driven into Deep Saturation"]:::nightBranch
    Q1_ON --> LED_ON["Collector Sinks ~24.4 mA Total<br/>Both Yellow & Green Branches Active"]:::nightBranch
    LED_ON --> LoopBack

    LoopBack -.-> Sense
```

---

## 🛠️ Hardware Assembly & Real-World Prototyping

The physical prototype was constructed, wired, and tested on a standard breadboard using discrete through-hole components.

<div align="center">
  <table>
    <tr>
      <td align="center" width="50%">
        <img src="assets/breadboard_top_view.jpg" alt="Breadboard Top View" width="400"/>
        <br/>
        <em><b>Figure 2:</b> Top-down layout of breadboard wiring, discrete components, and 9V power block.</em>
      </td>
      <td align="center" width="50%">
        <img src="assets/hardware_setup.jpg" alt="Complete Hardware Setup" width="400"/>
        <br/>
        <em><b>Figure 3:</b> Complete bench test setup with power distribution harness.</em>
      </td>
    </tr>
  </table>
</div>

### Night Activation Demonstration

When ambient light levels drop below the programmed threshold ($R_{\text{LDR}} > 55.3\,\text{k}\Omega$), both LED branches activate immediately:

<div align="center">
  <img src="assets/led_glowing_dark.jpg" alt="Dark Environment Operation" width="600"/>
  <p><em>Figure 4: Automated activation in low-light environment — dual branches active at rated forward currents.</em></p>
</div>

---

## 📋 Bill of Materials (BOM)

| Designator | Component Specification | Package | Qty | Functional Role |
|:----------:|:------------------------|:-------:|:---:|:----------------|
| **$B_1$** | $9\,\text{V}$ Alkaline Battery | 6F22 Snap | 1 | Primary DC Power Source |
| **$D_3$** | 1N4007 Standard Rectifier | DO-41 | 1 | Reverse polarity blocking ($V_{\text{RRM}} = 1000\,\text{V}$) |
| **$\text{LDR}_1$** | Cadmium Sulfide (CdS) Photoresistor | $5\,\text{mm}$ Radial | 1 | Light intensity transducer |
| **$R_1$** | $4.7\,\text{k}\Omega \pm 5\%$, $0.25\,\text{W}$ | Axial CFR | 1 | Potential divider lower arm reference |
| **$R_2$** | $4.7\,\text{k}\Omega \pm 5\%$, $0.25\,\text{W}$ | Axial CFR | 1 | Driver stage pull-up resistor |
| **$R_3, R_4$** | $330\,\Omega \pm 5\%$, $0.25\,\text{W}$ | Axial CFR | 2 | Series LED current limiters |
| **$Q_3$** | BC547 NPN Bipolar Transistor | TO-92 | 1 | Small-signal sensor inverter stage |
| **$Q_1$** | PN2222A NPN Bipolar Transistor | TO-92 | 1 | High-current output sinking switch |
| **$D_1, D_5$** | Diffused Yellow LED | $5\,\text{mm}$ Through-hole | 2 | Optical indicator branch 1 ($V_F \approx 2.0\,\text{V}$) |
| **$D_2, D_4$** | Diffused Green LED | $5\,\text{mm}$ Through-hole | 2 | Optical indicator branch 2 ($V_F \approx 2.1\,\text{V}$) |

---

## 🔬 Experimental Observations

| Ambient Illumination Regimes | Sensor Resistance ($R_{\text{LDR}}$) | Transistor $Q_3$ State | Transistor $Q_1$ State | Indicator Status | Total Current |
|:---|:---:|:---:|:---:|:---:|:---:|
| **Direct Incandescent / Torch** | $\sim 500\,\Omega - 2\,\text{k}\Omega$ | Saturated ($V_{CE} \approx 0.15\text{V}$) | Cutoff | Extinguished | $\sim 1.7\,\text{mA}$ |
| **Ambient Classroom / Lab Ambient** | $\sim 8\,\text{k}\Omega - 25\,\text{k}\Omega$ | Saturated | Cutoff | Extinguished | $\sim 1.9\,\text{mA}$ |
| **Twilight / Partial Shadowing** | $\sim 45\,\text{k}\Omega - 60\,\text{k}\Omega$ | Linear Active Transition | Linear Active | Incipient Glow | $\sim 5 - 14\,\text{mA}$ |
| **Total Darkness (Fully Covered)** | $> 250\,\text{k}\Omega$ | Cutoff ($V_{BE} < 0.2\text{V}$) | Saturated ($V_{CE} \approx 0.18\text{V}$) | High Intensity | $\sim 26.1\,\text{mA}$ |

---

## ⚖️ Engineering Trade-Offs & Future Enhancements

| # | Limitation | Current Implementation | Proposed Enhancement | Expected Improvement |
|:--:|:-----------|:----------------------|:---------------------|:---------------------|
| 1 | **Fixed Threshold** | $R_1 = 4.7\,\text{k}\Omega$ fixed reference — switching point determined by component tolerance | Replace $R_1$ with a **10 kΩ multi-turn cermet potentiometer** | Fully user-adjustable Lux switching point; on-site calibration |
| 2 | **Threshold Flicker** | Gradual BJT linear-active transition near boundary — LEDs oscillate at twilight | Add **LM393 voltage comparator** with positive feedback (Schmitt hysteresis) | Snap-action switching; eliminates flicker band entirely |
| 3 | **LED-Only Load** | Collector sink current limited to small DC indicator arrays (~25 mA) | Replace LED branch with **5V relay** or **IRF540N Power MOSFET** gate drive | Switch 230V AC mains loads — fans, lamps, any household appliance |
| 4 | **Slow LDR Response** | CdS photoresistor response time ~20–50 ms; hysteresis in recovery | Replace LDR with **BPW34 PIN photodiode** + transimpedance amplifier | Sub-millisecond response; linear lux-to-voltage conversion |
| 5 | **Breadboard Fragility** | Loose contact joints; susceptible to vibration and intermittent opens | Migrate design to **custom PCB** (EasyEDA / KiCad) with soldered joints | Mechanical robustness; long-term deployment reliability |

---

## 👥 Authors & Academic Credits

Developed under the **Department of Electronics and Communication Engineering (ECE)**:
**Institute of Engineering & Management (IEM), Kolkata**

| Member | Roll Number | Enrollment Number | Academic Session |
|:-------|:-----------:|:-----------------:|:----------------:|
| **Mayukh Bhattacharjee** | 240 | 12025052003258 | 2026 – 2027 |
| **Siddhartha Saha** | 237 | 12025052003255 | 2026 – 2027 |

* **Course:** Bachelor of Technology (B.Tech)
* **Laboratory:** Analog Electronics Laboratory
* **Mentorship:** Sutapa Ray Adhikary Mam

---

## 📄 License

This repository and all associated documentation, schematics, and media are released under the [MIT License](LICENSE).

```text
Copyright (c) 2026-2027 Mayukh Bhattacharjee & Siddhartha Saha
```
