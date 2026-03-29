<div align="center">

<img src="game/Assets/sollertia_purple.svg" alt="Sollertia Logo" width="360">

**Measuring Fine Motor Control in XR**

[![Unity](https://img.shields.io/badge/Unity-6-000000?style=for-the-badge&logo=unity&logoColor=white)](https://unity.com)
[![Rust](https://img.shields.io/badge/Rust-Dashboard-DEA584?style=for-the-badge&logo=rust&logoColor=white)](https://rust-lang.org)
[![Arduino](https://img.shields.io/badge/Arduino-Hardware-00979D?style=for-the-badge&logo=arduino&logoColor=white)](https://arduino.cc)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue?style=for-the-badge)](LICENSE)

[Overview](#overview) | [Metrics](#metrics) | [System](#system) | [Sensing](#sensing) | [Installation](#installation) | [History](#history) | [Team](#team)

</div>

---

## Table of Contents

- [Overview](#overview)
- [Metrics](#metrics)
- [System](#system)
- [Sensing](#sensing)
- [Current Study](#current-study)
- [Data Output](#data-output)
- [Installation](#installation)
- [Repository Structure](#repository-structure)
- [History](#history)
- [References](#references)
- [Team](#team)
- [License](#license)

---

## Overview

Sollertia is a system for measuring **fine motor control** in XR using task-based interaction and wearable force input.

It replicates the same button-based task in a physical setup and in XR, and compares performance across both environments.

Users respond to light-up targets by pressing them as quickly and accurately as possible. We log behavioral and force data to capture how people move, react, and apply pressure during interaction.

---

## Metrics

### Behavioral (XR + Physical)

| Metric | Description |
|--------|-------------|
| **Reaction Time** | Time from target onset to response |
| **Completion Time** | Total time to complete press |
| **Spatial Error** | Distance from target center |
| **Variability** | Consistency across trials |
| **Movement Trajectory** | Path of hand/finger during reach |

### Wearable (FSR on Index Finger)

| Metric | Description |
|--------|-------------|
| **Force Magnitude** | Peak pressure during press |
| **Force Over Time** | Temporal force profile |
| **Pressure Variability** | Stability of force application |

---

## System

| Component | Description |
|-----------|-------------|
| **Physical Board** | LED targets with matched layout |
| **XR Version** | Meta Quest 3 with hand tracking, matched timing |
| **FSR Sensor** | Finger-mounted force sensing |
| **Logging Pipeline** | Synchronized timing and force data |

### Architecture

```
                    +------------------+
                    |   Meta Quest 3   |
                    |   XR Application |
                    +--------+---------+
                             |
                             | WebSocket
                             |
+------------------+         |         +------------------+
|    Wearable      +---------+---------+    Dashboard     |
|    Hardware      |   USB Serial      |    (Rust)        |
|    (FSR)         |                   |                  |
+------------------+                   +------------------+
```

---

## Sensing

### Current

- **FSR sensor** on index finger to measure press force and pressure over time

### In Progress

- **EMG integration** using OpenBCI and BrainFlow to capture muscle activation during interaction

---

## Current Study

We compare performance between a physical task and its XR equivalent to evaluate whether XR interaction can capture meaningful fine motor behavior.

**Direction:** This project focuses on measuring motor behavior in controlled tasks.

---

## Data Output

Each trial logs:

| Field | Description |
|-------|-------------|
| `target_id` | Which target was pressed |
| `stimulus_time` | When target lit up |
| `press_time` | When press was registered |
| `reaction_time` | Time to respond |
| `accuracy` | Spatial error from center |
| `trajectory` | Movement path (XR only) |
| `force_signal` | FSR readings over time |

---

## Installation

### Prerequisites

- Unity 6 (6000.3.7f1 or later)
- Rust toolchain (1.70+)
- Arduino IDE (2.0+)
- Meta Quest 3 with developer mode enabled

### XR Application

1. Clone the repository:

   ```bash
   git clone https://github.com/anaya33/Sollertia.git
   cd Sollertia
   ```

2. Open `game/` in Unity Hub

3. Install required packages:
   - XR Interaction Toolkit
   - OpenXR Plugin
   - XR Hands

4. Open `Assets/SollertiaDemo.unity` and press Play

For Quest 3 deployment, see [`QUEST_DEPLOYMENT.md`](game/Assets/Scripts/Sollertia/QUEST_DEPLOYMENT.md).

### Hardware

```
FSR (Index) → A0
```

Upload `hardware/hardware.ino` via Arduino IDE.

### Dashboard

```bash
cd dashboard
cargo build --release
cargo run --release
```

---

## Repository Structure

```
Sollertia/
|
|-- game/                    # Unity XR application
|   |-- Assets/
|   |   |-- Scripts/Sollertia/
|   |   |-- SollertiaDemo.unity
|
|-- dashboard/               # Rust dashboard
|   |-- Cargo.toml
|   |-- crates/
|
|-- hardware/                # Arduino firmware
|   |-- hardware.ino
|
|-- paper/                   # Research whitepaper (LaTeX)
|
|-- docs/                    # GitHub Pages site
|
|-- README.md
|-- LICENSE
```

---

## History

### Origin: UGAHacks XI

Sollertia began as a hackathon project at **UGAHacks XI** under the name "Tactilis." The original vision was a mixed-reality rehabilitation tool for stroke recovery, combining XR interaction with wearable pressure sensing.

**Original Hackathon Team:**
- Anaya Yorke
- David Salas C.
- Garret S. Stand
- Mathias Sosa

### Evolution to Research

After the hackathon, the project evolved from a rehabilitation-focused prototype into a research system for studying fine motor control. The scope shifted from clinical rehabilitation to fundamental research on comparing motor behavior across physical and XR environments.

**What Changed:**
- Removed rehabilitation-specific framing
- Focused on controlled motor measurement
- Added physical task condition for comparison
- Expanded sensing capabilities (EMG in progress)

The name changed from "Tactilis" to "Sollertia" (Latin for "skill with hand") to reflect the research focus on motor skill measurement.

---

## References

1. Schoen et al., 2025. *From Pegs to Pixels: A Comparative Analysis of the Nine Hole Peg Test and a Digital Copy Drawing Test for Fine Motor Control Assessment.*
   [PDF](https://thomaskosch.com/wp-content/papercite-data/pdf/schoen2025from.pdf)

2. Lu et al., 2019. *Modeling Endpoint Distribution of Pointing Selection Tasks in Virtual Reality Environments.*
   [PDF](https://xueshilu.github.io/papers/Modeling%20Endpoint%20Distribution%20of%20Pointing%20Selection%20Tasks%20in%20Virtual%20Reality%20Environments.pdf)

3. Chen et al., 2024. *Metrics of Motor Learning for Analyzing Movement Mapping in Virtual Reality.*
   [PDF](https://www.difeng.me/papers/24_Metrics.pdf)

4. Wei et al., 2019. *Accurate and Low-Latency Sensing of Touch Contact on Any Surface with Finger-Worn IMU Sensor.*
   [PDF](https://xiaoying-wei.github.io/TappingRing.pdf)

5. Schneider et al., 2021. *Accuracy Evaluation of Touch Tasks in Commodity Virtual and Augmented Reality Head-Mounted Displays.*
   [PDF](https://pokristensson.com/pubs/SchneiderEtAlSUI2021.pdf)

---

## Team

### Current Research Team

| Name | Role | GitHub |
|------|------|--------|
| **Anaya Yorke** | Lead Researcher | [@anaya33](https://github.com/anaya33) |
| **Garret S. Stand** | Software Engineering | [@gstand](https://github.com/gstand) |

### Original Hackathon Team (UGAHacks XI)

- Anaya Yorke
- David Salas C.
- Garret S. Stand
- Mathias Sosa

---

## License

This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE) file for details.

---

<div align="center">

**Sollertia** - *Latin for "skill with hand"*

[View Whitepaper](https://anaya33.github.io/Sollertia/) | [Report Issues](https://github.com/anaya33/Sollertia/issues)

</div>
