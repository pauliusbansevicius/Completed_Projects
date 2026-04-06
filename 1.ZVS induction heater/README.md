# ZVS Induction Heater Manual

This repository contains the documentation and technical specifications for a **Zero Voltage Switching (ZVS)** induction heater.

---

## Bill of Materials (B.O.M) 
<div align="center">
  <img src="ZVS%20IH%20schematic,%20waveforms%20and%20FFT.png" 
       alt="ZVS Driver Analysis" 
       style="border: 2px solid black; display: block;">
  <p><i>Figure 1: ZVS driver schematic, output waveforms, and FFT analysis.</i></p>
</div>

| Component | Specification | Description |
| :--- | :--- | :--- |
| **V1** | 12V DC | Power supply (PC PSU or car battery). |
| **M1, M2** | IRFP250N | NMOSFETs for switching the resonant circuit. |
| **L1** | 2-3µH | Air wound coil: 10AWG wire, 5cm diameter, 8-10 turns. |
| **L2, L3** | 100µH | 20A toroidal inductors (choke) to protect the PSU. |
| **C1 - C6** | 1µF (6µF total) | MKP10 polypropylene capacitors for high frequencies. |
| **D1, D2** | 12V Zener | Limits NMOSFET gate voltage to 12V. |
| **D3, D4** | MUR460 / UF4007 | UltraFast diodes with ~75ns recovery time. |
| **R1, R2** | 10kΩ 1W | Pull-down resistors to clear gate charge. |
| **R3, R4** | 470Ω 1W | Resistors to limit current to the NMOSFET gates. |

---

## Circuit Schematic & Analysis

<div align="center">
  <img src="ZVS%20IH%20schematic,%20waveforms%20and%20FFT.png" 
       alt="ZVS Driver Analysis" 
       style="border: 2px solid black; display: block;">
  <p><i>Figure 1: ZVS driver schematic, output waveforms, and FFT analysis.</i></p>
</div>

### Performance Parameters 
* **Efficiency:** Approximately 90%.
* **Idle Current:** 1-1.5A.
* **Heating Current:** Up to 20A.
* **Output:** AC voltage through the L1 coil heats the metal.

---

## How it Works
<div align="center">
  <img src="ZVS%20IH%20schematic,%20waveforms%20and%20FFT.png" 
       alt="ZVS Driver Analysis" 
       style="border: 2px solid black; display: block;">
  <p><i>Figure 1: ZVS driver schematic, output waveforms, and FFT analysis.</i></p>
</div>

The ZVS induction heater is a **self-oscillating parallel resonant inverter**.
ZVS induction heater can be split into two parts: **a parallel resonant circuit** and **a circuit that adds energy to the resonant circuit**


1.  **Power Application**: When the power supply is connected to the ZVS driver, the voltage potential increases on both sides of the resonant circuit and at the gates of the NMOSFETs.
2.  **Asymmetry and Startup**: Because real-world components are not perfect, one NMOSFET will always turn on slightly faster than the other. In the simulation, this is achieved by using slightly different values for resistors **R3** and **R4**. Without this intentional imbalance in a simulation, both NMOSFETs might turn on halfway simultaneously, shorting the power supply and preventing the resonant circuit from oscillating.
3.  **Initial Switching**: Once one NMOSFET turns on—for example, **M2**—it connects the **left** side of the resonant circuit to 0V (ground). At the same moment, **M1** is kept off because its gate is pulled to ground through diode **D3**.
4.  **Initiating Oscillation**: The voltage at the drain of **M1** remains high because **M1** is not connected to ground. This high potential on the **right** side of the resonant circuit initiates the oscillations.
5.  **Resonant Cycle**: The voltage within the resonant circuit begins to rise and then fall. When the voltage on the **right** side of the resonant circuit drops to 0V, the gate of **M2** is pulled to ground through diode **D4**.
6.  **Alternating Cycles**: After **M2** closes, the potential at the gate of **M1** increases until **M1** turns on, which then shorts the **right** side of the resonant circuit to ground. This continuous switching maintains the ZVS oscillations. 
7.  **Efficiency and Heat**: The primary advantage of **Zero Voltage Switching (ZVS)** is that the NMOSFETs are only switched when the voltage across them is at 0V (potential between drain and source is 0V). Theoretically, this makes the circuit nearly 100% efficient. However, in practice, oscillations are not perfect; the NMOSFETs still generate heat and require substantial heatsinks for cooling. Practical efficiency usually ranges between **70-90%**.
8.  **Induction Heating**: The high current flowing through the work coil (**L1**) creates a powerful magnetic field. When a piece of metal is placed inside **L1**, eddy currents are induced within it, which rapidly heat the metal.



