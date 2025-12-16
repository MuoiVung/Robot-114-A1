# Epson C3-A600S Pick & Place System with JetBot Integration

### 👨‍🎓 Project Team & Roles

| Role | Name | Student ID | Responsibilities |
| :--- | :--- | :--- | :--- |
| **Mechanical Engineer (ME)** | **To Nguyen Tan Phuong** | M11451804 | Fixture Design (STL/CAD), Tolerance Analysis, JetBot AGV Programming & Navigation Logic |
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

This project implements an automated manufacturing cell combining a fixed **Epson C3-A600S (6-Axis)** robot arm with a mobile **JetBot AGV**. The system performs high-speed Pick & Place operations while the JetBot handles the autonomous transport of materials to the fixture station.

## 📂 Repository Structure

The project is organized into three main modules:

### 1. 🤖 `Epson/` (Robot Arm Control)
Contains all source code and mechanical design files for the Epson C3 Manipulator.
* **`Robot_Production.zip`**: Source code for the physical robot (Compatible with **EPSON RC+ 5.0**).
* **`Robot_Simulation.zip`**: Source code for the simulator (Compatible with **EPSON RC+ 7.0**).
* **`Robot_Workflow.vsdx`**: Logic flow diagram.
* **`alignment_fixture.stl`**: 3D printable file for the custom alignment fixture.

### 2. 🏎️ `Jetbot/` (AGV Navigation)
[cite_start]Contains Python scripts for the autonomous mobile robot[cite: 1].
* [cite_start]**`jetbot_main.ipynb`**: Main operation code handling Line Following (Yellow) and Obstacle Avoidance (Red)[cite: 1].
* [cite_start]**`JetBot_config_color.ipynb`**: Calibration tool with UI sliders to adjust HSV thresholds for line and obstacle detection[cite: 1].
* [cite_start]**`yellow_config.npy`** & **`red_config.npy`**: Saved NumPy configuration files storing color threshold values[cite: 1, 2].

### 3. 🖼️ `images/` (Documentation)
* **`align_design.jpg`**: Real-world photo of the machined alignment fixture.
* **`Wiring_Diagram.png`**, **`Simulation_Demo.gif`**, and other reference visuals.

---

## ⚙️ Hardware & Software Requirements

### Software Environments
1.  **Simulation:** EPSON RC+ 7.0 (Robot), Jupyter Lab (JetBot).
2.  **Production:** EPSON RC+ 5.0 (Robot Controller), Python 3.6+ (JetBot).

### Hardware Setup
* **Robot:** Epson C3-A600S (6-Axis).
* **Mobile Robot:** NVIDIA JetBot (Waveshare/SparkFun variant).
* **Controller:** Epson RC180.
* **Peripherals:** Remote I/O Control Box, Vacuum System, Custom Alignment Fixture.

---

## 🛠️ Mechanical Design: Alignment Fixture

The system utilizes a custom-designed alignment fixture to ensure precise repeatability when the JetBot delivers parts.

<p align="center">
  <img src="images/align_design.jpg" width="600" alt="Machined Alignment Fixture">
  <br>
  <em>(Actual machined fixture used in the cell - 3D file available in Epson/alignment_fixture.stl)</em>
</p>

---

## 🚀 Key Features

### 1. JetBot Autonomous Navigation
The mobile robot uses computer vision to navigate the factory floor:
* [cite_start]**Line Following:** Tracks a yellow path using HSV color filtering and PID control[cite: 1].
* **Obstacle Avoidance:** Detects red objects. [cite_start]If an obstacle is found, the robot executes a "blind run" maneuver to bypass it and searches for the line to merge back[cite: 1].
* [cite_start]**Far Detection:** Utilizes a "Check Far" algorithm (scanning pixels 90-224) to detect road curves early[cite: 1].

### 2. Smart Robot Auto-Calibration (2-Point Method)
Instead of the standard 3-point wizard, the Epson system uses custom SPEL+ algorithms (`UpdateTrayLocal`, `UpdateFixtureLocal`) to recalculate Local Coordinates:
* **2-Point Teaching:** Requires teaching only the **Origin** and one point on the **Y-Axis**.
* **Auto-Flip Logic:** Automatically detects reverse orientation and corrects by 180°.

### 3. Motion Optimization
* **Continuous Path (CP):** Extensive use of `Go ... CP` for smooth blending.
* **Parallel Processing:** Vacuum activation occurs *during* movement.

---

## 📥 Installation & Usage

### A. JetBot Setup
1.  Connect to the JetBot via Jupyter Lab.
2.  [cite_start]Open `Jetbot/JetBot_config_color.ipynb` to calibrate the camera for your specific lighting environment (Yellow for line, Red for obstacles)[cite: 1].
3.  Run `Jetbot/jetbot_main.ipynb` to start the autonomous loop.

### B. Epson Robot Setup
1.  **Simulation:** Extract `Epson/Robot_Simulation.zip` and open in RC+ 7.0.
2.  **Physical:** Extract `Epson/Robot_Production.zip`, import to RC+ 5.0, and configure I/O.
3.  **Calibration:** Perform the 2-point calibration routine before full-speed operation.

---

## 🔌 Wiring Diagram

<p align="center">
  <a href="images/Wiring_Diagram.png" target="_blank">
    <img src="images/Wiring_Diagram.png" width="800" alt="Wiring Diagram I/O to RC180">
  </a>
</p>

---
*This project was developed as part of the Autonomous Mobile Vehicles and Robots Introduction (Fall 2025) at National Taiwan University of Science and Technology.*
