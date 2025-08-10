# Breakout Board: Battery Charger & Fuel Gauge — Design Story and Choices

Designing a compact, reliable battery management breakout board isn’t just about cramming chips together — it’s about understanding the trade-offs behind every component choice.  
Here’s the thinking behind the **Battery Charger & Fuel Gauge Breakout Board** and why I chose the ICs I did.

## 1. The Battery Charger: BQ24040 vs. TP4056

The **TP4056** has earned its fame in the maker world — it’s cheap, easy to use, and well supported. But it hides a quirk that can cause headaches in certain designs.

**The TP4056 quirk:**
- Pre-charge threshold: ~3.0 V
- Fixed pre-charge (trickle charge) current: **10 mA**
- If your battery is below 3 V and you have an **LDO** pulling ~10 mA from the charger output (to power 3.3 V logic, for instance), the TP4056 will happily supply that current to the LDO… **and zero to the battery**.
- Result? The battery never rises above 3 V, and the charger never switches to constant current mode. Stalemate.

**How the BQ24040 solves it:**
- **Lower pre-charge threshold**: ~2.5 V (battery starts charging earlier in its discharge)
- **Configurable pre-charge current**: e.g., 50 mA — plenty to both feed your LDO and still trickle energy into the cell.
- Additional perks:
  - Power path management: can supply load and charge battery simultaneously.
  - Status outputs for charge and fault indication.
  - Thermal regulation built in.

This means your system boots faster from a dead battery, and you don’t get stuck in a “never charging” deadlock.

## 2. The Fuel Gauge: BQ27621-G1 and the Calibration Dilemma

Battery fuel gauging is trickier than it looks. You can measure voltage, but it doesn’t tell the whole story about state of charge (SoC).

**Two main approaches:**
1. **Impedance Tracking** — Highly accurate, but requires painful calibration and “training” cycles to match the gauge to your specific cell.  
   Great for high-volume products, less great for quick prototypes or small-run boards.
2. **Voltage Correlation** — Uses cell voltage, temperature, and discharge patterns to estimate SoC. Less accurate over extreme conditions, but easy to set up.

The **BQ27621-G1** uses a **dynamic voltage correlation algorithm**:
- No complex calibration process.
- Good enough accuracy for most portable projects.
- Very low quiescent current — ideal for battery-powered devices.
- I²C interface for easy microcontroller integration.

For a breakout board aimed at prototyping and flexibility, avoiding impedance tracking’s long setup time is worth the small trade-off in accuracy.

## 3. Board Connections and Options

This board isn’t just two chips and some passives — it’s designed to be **flexible** in different setups.

**Key features:**
- **USB input** for charging.
- **Battery JST connector** for Li-Ion/Li-Po cells.
- **Load output** that stays powered even while charging (thanks to the BQ24040’s power path management).
- **I²C interface** (SDA, SCL) for reading fuel gauge data.
- **Configurable pre-charge current** via resistor selection.
- **Charge status pins** for LED indicators or MCU monitoring.

## 4. Why This Combo Works Well

The **BQ24040 + BQ27621-G1** pairing offers:
- Robust charging under low-voltage conditions.
- Accurate-enough battery state reporting without lengthy setup.
- Support for both charging and powering your project at the same time.
- Minimal parts count without sacrificing essential battery management features.

_Refer to the Design Document for the board configurations_
