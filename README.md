# Epson C3-A600S Pick & Place System & JetBot AMR

### 👨‍🎓 Project Team & Roles

| Role | Name | Student ID | Responsibilities |
| :--- | :--- | :--- | :--- |
| **Mechanical Engineer (ME)** | **To Nguyen Tan Phuong** | M11451804 | Fixture Design, Tolerance Analysis, 3D Modeling, Jetbot Programming |
| **Electrical Engineer (EE)** | **Nguyen Thao Huong** | M11451806 | I/O Wiring, Electrical Cabinet, Sensor Integration, Stacking Cycle Programming |
| **Robotics Engineer** | **Tran Viet Nam** | M11451805 | Pick and Place Programming, Simulation, Motion Logic |
---

<p align="center">
  <img src="images/Simulation_Demo.gif" width="100%" alt="Epson Robot Simulation Animation">
</p>

<p align="center">
  <details>
    <summary><strong>👁️ Click to view high-resolution static angles (Front & Isometric)</strong></summary>
    <br>
    <table align="center" border="0">
      <tr>
        <td align="center" width="50%">
          <img src="images/Simulation_Front.png" width="100%" alt="Simulation Front View">
          <br>
          <em>(Front View)</em>
        </td>
        <td align="center" width="50%">
          <img src="images/Simulation_Iso.png" width="100%" alt="Simulation Isometric View">
          <br>
          <em>(Isometric View)</em>
        </td>
      </tr>
    </table>
  </details>
</p>

## 📖 Introduction

This project is part of the **Autonomous Mobile Vehicles and Robots Introduction** course. It consists of two main modules working in a simulated manufacturing environment:

1.  **Epson Robot Station:** An automated control system for the **Epson C3-A600S (6-Axis)** robot paired with an **RC180 Controller**.
2.  **JetBot AMR:** An autonomous mobile robot responsible for transporting parts to the robot's fixture using vision-based navigation.

## 📂 Repository Structure

The repository is divided into folders for each hardware component:

### 1. `Epson/` (Robot Arm)
* **`Robot_Production.zip`**: Source code for the physical robot (Compatible with **EPSON RC+ 5.0**).
* **`Robot_Simulation.zip`**: Source code for the simulator (Compatible with **EPSON RC+ 7.0**).
* **`Robot_Workflow.vsdx`**: Original Visio file for logic modification.
* **`alignment_fixture.stl`**: 3D design file for the custom alignment fixture.

### 2. `Jetbot/` (Mobile Robot)
* **`jetbot_main.ipynb`**: Main control script (Line Following & Obstacle Avoidance).
* **`JetBot_config_color.ipynb`**: Color calibration tool for camera setup.
* **`yellow_config.npy`** & **`red_config.npy`**: Configuration files for color thresholds.

### 3. `images/`
* Contains project photos, diagrams, and simulation recordings.

---

## ⚙️ Hardware & Software Requirements

### Software Environments
* **Robot Simulation:** EPSON RC+ 7.0 (For advanced 3D visualization).
* **Robot Production:** EPSON RC+ 5.0 (Required for the physical RC180 controller).
* **JetBot:** Jupyter Lab (Python 3).

### Hardware Setup
* **Robot:** Epson C3-A600S (6-Axis Vertical Articulated).
* **Controller:** Epson RC180.
* **Mobile:** NVIDIA JetBot (Waveshare/SparkFun).
* **Peripherals:** Remote I/O Control Box, Vacuum Suction, Custom Alignment Fixture.

---

## 🛠️ Mechanical Design: Alignment Fixture

A custom fixture was designed to align parts delivered by the JetBot for the Epson robot to pick up accurately.

<p align="center">
  <img src="images/align_design.jpg" width="600" alt="Machined Alignment Fixture">
  <br>
  <em>(Actual machined fixture - STL file available in <code>Epson/alignment_fixture.stl</code>)</em>
</p>

---

## 🚀 Key Features

### Part 1: Epson Robot (Assembly)
* **Smart Auto-Calibration (2-Point Method):** Uses custom SPEL+ algorithms (`UpdateTrayLocal`, `UpdateFixtureLocal`) to teach work planes using only 2 points (Origin + Y-Axis), automatically correcting for orientation flip.
* **Motion Optimization:** Extensive use of `Go ... CP` and `Move ... CP` for smooth blending. Parallel processing allows vacuum activation *during* movement.
* **Operation Modes:** Includes Pick & Place Cycle and Vertical Stacking logic.

### Part 2: JetBot AMR (Transport)
* **Line Following:** Uses HSV color filtering (Yellow) to track the path.
* **Obstacle Avoidance:** Detects red obstacles. Upon detection, the robot executes a "Blind Run" to bypass the object and uses a "Far Detection" check to find the line again.
* **Easy Calibration:** Includes a GUI-based notebook to adjust color thresholds for different lighting environments.

---

## 📝 Robot Code Overview (SPEL+)

* **Calibration Core (`Setup.prg`)**: Functions to recalculate Tray and Fixture positions with auto-correction.
* **Operation Modules**:
    * `Feeder.prg`: Picks items with calculated Z-offsets.
    * `Fixture.prg`: Intermediate alignment station.
    * `Tray.prg`: Palletizing logic.
    * `Stacking.prg`: Vertical stacking logic.
* **Task Management (`Main.prg`)**: Runs a background `Monitor` task for safety/IO alongside the main `Cycle`.

---

## 📥 Installation & Usage

### A. Epson Robot
1.  **Simulation:** Extract `Epson/Robot_Simulation.zip` and open in EPSON RC+ 7.0. Press **F5** to run.
2.  **Physical:** Extract `Epson/Robot_Production.zip`, import to EPSON RC+ 5.0, configure I/O as per the diagram below, and calibrate using the 2-point method.

### B. JetBot
1.  Upload the contents of the `Jetbot/` folder to the robot.
2.  Run `JetBot_config_color.ipynb` to calibrate Yellow/Red thresholds.
3.  Run `jetbot_main.ipynb` to start the autonomous loop.

### 🔌 Wiring Diagram (Epson)
Connection diagram between the Remote I/O Box and the RC180 Controller terminals.

<p align="center">
  <a href="images/Wiring_Diagram.png" target="_blank">
    <img src="images/Wiring_Diagram.png" width="800" alt="Wiring Diagram I/O to RC180">
  </a>
</p>

---

## 🛡️ Safety Notes

* **Payload:** Ensure the `Weight` parameter in `Init.prg` matches the actual payload (1.5 kg).
* **Speed:** Reduce Speed/Accel to < 10% during the first run.
* **E-Stop:** Always keep the Emergency Stop button within reach.

---
*This project was developed as part of the Autonomous Mobile Vehicles and Robots Introduction (Fall 2025) at National Taiwan University of Science and Technology.*
