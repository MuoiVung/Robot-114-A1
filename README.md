-----

# Epson C3-A600S Pick & Place System

This repository contains the source code, logic workflows, and wiring diagrams for an automated Pick & Place and Stacking system using the **Epson C3-A600S (6-Axis)** robot and **RC180 Controller**.

The project features a robust **Smart Calibration Algorithm** that allows for rapid fixture and tray re-teaching using only 2 points, significantly reducing setup time while maintaining high accuracy.

## 📂 Repository Structure

  * **`Robot_workflow.png`**: Flowchart illustrating the logical operation of the system.
  * **`Wiring_Diagram.png`**: Hardware connection diagram between the I/O Box and RC180 Controller.
  * **`Robot_Production.zip`**: The complete EPSON RC+ 7.0 project source code for the **Physical Robot**.
  * **`Robot_Simulation.zip`**: The modified EPSON RC+ 7.0 project source code for the **Simulator** (Vacuum checks disabled).

-----

## ⚙️ Hardware & Software Requirements

  * **Robot:** Epson C3-A600S (6-Axis Vertical Articulated).
  * **Controller:** Epson RC180.
  * **IDE:** EPSON RC+ 7.0.
  * **Peripherals:**
      * Remote I/O Control Box (Start, Stop, Reset).
      * Vacuum Gripper with suction sensors.
      * Feeder, Fixture, and Pallet Tray.

### 🔌 Wiring Diagram

The following diagram details the connection between the remote I/O button box and the RC180 controller terminals.

*(Image: Wiring diagram for I/O Box to RC180 Controller)*

-----

## 🚀 Key Features

### 1\. Smart Auto-Calibration (Vector-Based)

Instead of standard 3-point teaching, this system uses a custom SPEL+ algorithm (`UpdateTrayLocal`, `UpdateFixtureLocal`) to recalculate Local Coordinates using vector mathematics.

  * **2-Point Teaching:** Requires only the **Origin** and one point on the **Y-Axis**.
  * **Auto-Correction:** Includes logic to automatically detect if the Y-axis was taught in reverse and flips the orientation by 180° automatically.
  * **Math Logic:** Uses `Atan2` to calculate angles and preserves the Z-height/Tilt (V, W) from a master local definition.

### 2\. Motion Optimization

  * **Continuous Path (CP):** Extensively uses `Go ... CP` and `Move ... CP` for smooth transitions without stopping at intermediate points.
  * **Parallel Processing:** Vacuum is triggered *during* motion using syntax like `Move ... ! D30; On Vacuum !` to reduce cycle time.
  * **Soft Compliance:** Uses `SoftCP` during the placing phase at the Tray to compensate for minor misalignments and prevent damage.

### 3\. Dual Operation Modes

The system supports two distinct cycles triggered by external I/O:

  * **Pick & Place Cycle:** Feeder -\> Fixture -\> Tray (Palletizing).
  * **Stacking Cycle:** Stacking multiple items vertically.

-----

## 🔄 Logic Workflow

The system operates via a non-stop `Monitor` task that handles safety (Stop/Reset) and a main `Cycle` task for robot motion.

*(Image: Flowchart of the robot program logic)*

-----

## 📝 Code Overview

### 1\. Calibration (`Setup.prg`)

Handles the mathematical recalculation of Locals 2 and 3.

  * **`UpdateTrayLocal`**: Updates the Tray position.
  * **`UpdateFixtureLocal`**: Updates the Fixture position with "Auto-Flip" logic to correct orientation errors.

### 2\. Operation Modules

  * **`Feeder.prg`**: picks items with calculated Z-offsets. Uses `(itemIndex - 1) Mod 3` logic for layout handling.
  * **`Fixture.prg`**: Handles intermediate alignment.
  * **`Tray.prg`**: Uses `Pallet` definitions. Distinct pallets are defined for "Tokens" and "Blocks" to handle different rotation requirements (Pallet 1 vs Pallet 2).
  * **`Stacking.prg`**: Specialized logic for vertical stacking operations.

### 3\. Task Management (`Main.prg`)

  * **`Monitor` Task**: Runs in the background (`NoPause`). It watches for:
      * **RESET (Input 7):** Aborts cycle, homes robot, and re-initializes.
      * **STOP (Input 4):** Quits the current cycle safely.
      * **START:** Triggers the selected cycle.

-----

## ⚠️ Simulation vs. Production Code

There are two versions of the code provided in the releases:

1.  **Robot\_Production:**

      * Contains the line: `Wait Sw(SensorVacuum) = On`
      * **Use this for the real robot.** It ensures the robot waits for physical vacuum confirmation before moving.

2.  **Robot\_Simulation:**

      * The line `Wait Sw(SensorVacuum) = On` is **commented out**.
      * **Use this for EPSON RC+ Simulator.** Since the simulator has no physical sensors, enabling the wait command would cause the program to hang indefinitely.

-----

## 📥 Installation

1.  Download the appropriate `.zip` file.
2.  Open **EPSON RC+ 7.0**.
3.  Go to **Project** -\> **Import**.
4.  Select the extracted folder.
5.  Build and Run.

-----

## 🛡️ Safety Note

  * Ensure the **Weight** parameter in `Init.prg` matches your actual payload (currently set to `1.5` kg).
  * Always test the **Calibration** functions at slow speeds after re-teaching points.
  * Verify I/O wiring matches the `Globals.inc` definitions before connecting power.
