# ZVS Induction Heater Manual

<div align="center">
  <img src="ZVS%20IH%20schematic,%20waveforms%20and%20FFT.png" alt="Left Image" width="48%">
  <img src="Glowing%20red%20alligator%20clip%20in%20darkness.jpg" alt="Right Image" width="48%">
</div>

This folder contains the documentation and technical specifications for a **Zero Voltage Switching (ZVS)** induction heater.

<div align="center">
<table style="border: 4px solid red; border-collapse: collapse;">
  <tr>
    <td style="padding: 20px; background-color: #fff5f5; color: #b91c1c; border: 4px solid red;">
      <p align="center">
        ❗❗❗❗ <strong>WARNING</strong> ❗❗❗❗
      </p>
      <p>
        If you are using this documentary as a tutorial to build your own induction heater, <strong>PLEASE</strong> use a PSU or any other power supply that has shorting protection. I can guarantee that at first startup your ZVS induction heater <strong>WILL</strong> fail to oscillate and short the power supply. A PSU will just safely turn off without damaging any components. If you use a car battery or any other power source with no shorting protection it will kill both of your MOSFETs instantly, potentially igniting, exploding and sending shrapnel everywhere. <strong>You have been warned.</strong>
      </p>
    </td>
  </tr>
</table>
</div>

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
| **V1** | 12V DC | Power supply (PC PSU or car battery(READ WARNING AT THE TOP OF THE PAGE). |
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
  <p><i>Figure 2: ZVS driver schematic, output waveforms, and FFT analysis.</i></p>
</div>

### Performance Parameters 
* **Input Voltage:** 12V.
* **Idle Current:** 1-1.5A.
* **Heating Current:** Up to 20A.
 * **Efficiency:** Approximately 90%.

---

## How it Works
<div align="center">
  <img src="ZVS%20IH%20schematic,%20waveforms%20and%20FFT.png" 
       alt="ZVS Driver Analysis" 
       style="border: 2px solid black; display: block;">
  <p><i>Figure 3: ZVS driver schematic, output waveforms, and FFT analysis.</i></p>
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

---
## Problems encountered

Here are all the problems I encountered before finally arriving to my final version (V2). All of the problems from V1 have been addressed or found to be inconsequential.

1. **Inductor chokes L2 and L3 did not have enough inductance** - My ZVS circuit was backfeeding 16V bursts back to my 12V supply. This could have damaged the decoupling capacitors inside the PSU at the +12V output. At first i included a 4700uF decoupling capacitor to short the AC from 12V DC bias to ground (also to add an energy tank for startup), but in V2 I just increased the inductance from 33uH to 100uH. The problem was rectified immediately.
2. **Main work coil was overheating (inconsequential)** - ideally the main work coil should have been made from copper tubing, which would have allowed the work coil to get way hotter. Additionally it could have had water running through the tube to remove the overheating problem entirely. Since V2s  work coil is made from 10awg wire it can only reach a certain temperature before the insulation starts to melt. It is worth mentioning that the wire was heating up due to the workpiece radiating heat to the coil and not high currents heating it up. Testing V2 revealed that i can heat a workpiece for about 5 minutes before having to cool down the work coil. This problem was found to be inconsequential, because i dont have to keep the induction heater on for a long time, since my main goal was to test metal heating capabilities. For that goal 5 minutes is more than enough (at average, bigger workpieces that fit snuggly into the 5cm diameter coil get bright red around the 2-3 minute mark)
3. **Capacitor bank capacity was way too low** - when building prototype V1 I used 6x150nF capacitors. The capacitance was way too low, resulting in the ZVS induction heater unable to heat workpieces above 150-200 celsius. For V2 i used 6x1uF capacitors (6.6x larger compared to V1) which proved to be sufficient in heating metal until it gets bright red.
4. **Using a prototyping board as a base (inconsequential for V2)** - in hindsight everything should have been made by free-forming it by soldering component legs to other component legs directly or with floating bus bars. V1s board buldged due to me having to resolder the main work coil many times due to troubleshooting. Since 10awg wire has a ton of thermal mass, this resulted in the prototyping board heating up to high temperatures, thus inducing stress in the board and buldging the middle upwards. This problem is inconsequential in V2, because i was building it as a final product rather than a prototype / first test.
5. **Both NMOSFETs turning on half way and shorting the power supply** - this is a very common problem with all ZVS circuits. V1 failed to start oscillating even after a week of troubleshooting. The cause identified was several cold solder joints, which were killing the oscillation and shorting the power supply (thankfully PSUs just turn off rather than exploding). Reflowing all the joints and applying fresh solder fixed this issue. After experiencing this problem with V1, I took more care into V2 by soldering the joints at a higher temperature (around 430-450 celsius). V2 started up on first try





