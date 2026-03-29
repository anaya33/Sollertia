<div align="center">

<img src="game/Assets/sollertia_purple.svg" alt="Sollertia Logo" width="360">

# Sollertia

**Quantifying Fine Motor Control in Extended Reality**

[![Unity](https://img.shields.io/badge/Unity-6-000000?style=for-the-badge&logo=unity&logoColor=white)](https://unity.com)
[![Rust](https://img.shields.io/badge/Rust-Dashboard-DEA584?style=for-the-badge&logo=rust&logoColor=white)](https://rust-lang.org)
[![Arduino](https://img.shields.io/badge/Arduino-Hardware-00979D?style=for-the-badge&logo=arduino&logoColor=white)](https://arduino.cc)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue?style=for-the-badge)](LICENSE)

[Overview](#overview) | [System](#system-architecture) | [Metrics](#metrics) | [Methods](#methods) | [Installation](#installation) | [Whitepaper](#whitepaper) | [Team](#team)

</div>

---

## Table of Contents

- [Overview](#overview)
- [Research Context](#research-context)
- [System Architecture](#system-architecture)
  - [XR Application](#xr-application)
  - [Wearable Hardware](#wearable-hardware)
  - [Clinical Dashboard](#clinical-dashboard)
- [Metrics](#metrics)
  - [Behavioral Metrics](#behavioral-metrics)
  - [Force Metrics](#force-metrics)
- [Methods](#methods)
  - [Task Design](#task-design)
  - [Data Collection](#data-collection)
  - [Analysis Pipeline](#analysis-pipeline)
- [Installation](#installation)
  - [Prerequisites](#prerequisites)
  - [XR Application Setup](#xr-application-setup)
  - [Hardware Setup](#hardware-setup)
  - [Dashboard Setup](#dashboard-setup)
- [Repository Structure](#repository-structure)
- [Whitepaper](#whitepaper)
- [Contributing](#contributing)
- [References](#references)
- [Team](#team)
- [License](#license)

---

## Overview

Sollertia is a research system for measuring **fine motor control** in extended reality (XR) environments using task-based interaction and wearable force sensing.

The system replicates a standardized button-pressing task in both physical and XR conditions, enabling direct comparison of motor performance across environments. By combining behavioral metrics (reaction time, accuracy, movement trajectory) with continuous force data from finger-mounted sensors, Sollertia provides a comprehensive characterization of upper-limb motor function.

### Research Questions

1. Can XR-based motor tasks capture clinically meaningful fine motor behavior?
2. How does motor performance in XR compare to equivalent physical tasks?
3. What role does force modulation play in characterizing motor control quality?

---

## Research Context

Fine motor control assessment is critical for rehabilitation following neurological events such as stroke, where disrupted neural pathways commonly affect hand dexterity and coordination. Traditional assessment methods (e.g., Nine Hole Peg Test, Box and Block Test) provide limited quantitative insight and lack the temporal resolution needed to characterize movement dynamics.

Virtual reality-based rehabilitation has shown promise for improving motor recovery through engaging, repetitive practice that supports neuroplasticity. However, validating that XR interactions accurately reflect real-world motor capabilities remains an open research challenge.

Sollertia addresses this gap by:

- **Standardizing task conditions** across physical and virtual environments
- **Capturing high-resolution temporal data** on movement and force
- **Enabling objective, quantitative comparison** of motor performance

### Theoretical Foundation

The system design draws on established motor control principles:

| Principle | Application in Sollertia |
|-----------|-------------------------|
| **Fitts' Law** | Target size and distance parameters follow established speed-accuracy tradeoffs |
| **Motor Learning Theory** | Repeated trials enable assessment of skill acquisition and variability reduction |
| **Force Control Models** | FSR data captures grip force modulation and stability |

---

## System Architecture

Sollertia consists of three integrated components:

```
                    +------------------+
                    |   Meta Quest 3   |
                    |   XR Application |
                    +--------+---------+
                             |
                             | WebSocket
                             |
+------------------+         |         +------------------+
|    Wearable      +---------+---------+    Clinical      |
|    Hardware      |   USB Serial      |    Dashboard     |
|    (FSR Glove)   |                   |    (Rust)        |
+------------------+                   +------------------+
```

### XR Application

The Unity-based XR application presents a button-pressing task where visual targets appear on a virtual surface. Built for Meta Quest 3 with hand tracking (no controllers required).

**Key Features:**

- AR passthrough mode for mixed reality interaction
- Configurable session parameters (duration, target count, difficulty)
- Real-time event logging with millisecond precision
- Hand tracking for natural finger interaction

**Technical Stack:**

- Unity 6 (6000.3.7f1) with Universal Render Pipeline
- OpenXR + XR Interaction Toolkit
- WebSocket client for real-time data streaming

### Wearable Hardware

A finger-mounted sensor system using force-sensing resistors (FSRs) to capture pressure data during interaction.

**Specifications:**

| Component | Details |
|-----------|---------|
| Microcontroller | ELEGOO UNO R3 (Arduino-compatible) |
| Sensors | 2x FSR402 on index and middle fingers |
| Sampling Rate | 10 Hz |
| Communication | USB Serial (9600 baud) |
| Data Format | 16-bit pressure values per channel |

**Why Index and Middle Fingers?**

These digits are primary effectors for precision grip and fine manipulation tasks. They provide:

- High functional relevance for daily activities
- Reliable force measurement points
- Established clinical significance in motor assessment

### Clinical Dashboard

A Rust-based application that aggregates data from both the XR application and wearable hardware, providing real-time visualization and session management for clinicians.

**Capabilities:**

- Real-time force and event visualization
- Session configuration and control
- Data export for offline analysis
- Longitudinal progress tracking

---

## Metrics

### Behavioral Metrics

Captured from the XR application during task performance:

| Metric | Description | Unit |
|--------|-------------|------|
| **Reaction Time** | Interval from target onset to movement initiation | ms |
| **Movement Time** | Duration from movement start to target contact | ms |
| **Completion Time** | Total time from target onset to successful press | ms |
| **Spatial Error** | Euclidean distance from fingertip to target center at contact | mm |
| **Movement Trajectory** | 3D path of hand/finger during reach | Vector3[] |
| **Trial Variability** | Standard deviation of metrics across trials | varies |

### Force Metrics

Captured from the wearable FSR sensors:

| Metric | Description | Unit |
|--------|-------------|------|
| **Peak Force** | Maximum pressure during press | N (calibrated) |
| **Force Onset Time** | Time from contact to force threshold | ms |
| **Force Duration** | Time force exceeds threshold | ms |
| **Force Variability** | Coefficient of variation across trials | % |
| **Force Profile** | Temporal force curve during press | N vs. time |

### Derived Metrics

| Metric | Computation | Significance |
|--------|-------------|--------------|
| **Throughput** | ID / MT (Fitts' Law) | Overall motor efficiency |
| **Accuracy Index** | 1 - (error / target_radius) | Precision of targeting |
| **Force Stability** | 1 / CV(force) | Consistency of force application |

---

## Methods

### Task Design

The button-pressing task follows a discrete pointing paradigm:

1. **Fixation**: Brief pause before target onset
2. **Target Onset**: Visual target illuminates at randomized location
3. **Reach**: Participant moves hand toward target
4. **Contact**: Fingertip makes contact with target surface
5. **Press**: Force applied until threshold reached
6. **Feedback**: Visual/auditory confirmation of successful press

**Task Parameters:**

| Parameter | Default | Range |
|-----------|---------|-------|
| Session Duration | 45 s | 30-120 s |
| Target Count | 9 | 4-16 |
| Target Diameter | 40 mm | 20-60 mm |
| Inter-target Distance | 80-150 mm | configurable |
| Force Threshold | 1.5 N | 0.5-3.0 N |

### Data Collection

Each trial generates a synchronized data record:

```
Trial Record
├── trial_id: uint32
├── target_id: uint8
├── stimulus_time: timestamp_ms
├── movement_onset_time: timestamp_ms
├── contact_time: timestamp_ms
├── press_time: timestamp_ms
├── release_time: timestamp_ms
├── contact_position: Vector3
├── target_position: Vector3
├── trajectory: Vector3[]
└── force_signal: uint16[] (10 Hz)
```

### Analysis Pipeline

1. **Preprocessing**: Timestamp alignment, outlier removal, signal filtering
2. **Feature Extraction**: Compute behavioral and force metrics per trial
3. **Aggregation**: Session-level statistics (mean, SD, trends)
4. **Comparison**: Physical vs. XR condition analysis
5. **Visualization**: Performance dashboards and trajectory plots

---

## Installation

### Prerequisites

- Unity 6 (6000.3.7f1 or later)
- Rust toolchain (1.70+)
- Arduino IDE (2.0+)
- Meta Quest 3 with developer mode enabled

### XR Application Setup

1. Clone the repository:

   ```bash
   git clone https://github.com/anaya33/Sollertia.git
   cd Sollertia
   ```

2. Open `game/` in Unity Hub

3. Install required packages via Package Manager:
   - XR Interaction Toolkit
   - OpenXR Plugin
   - XR Hands

4. Configure XR settings:
   - Enable OpenXR in XR Plug-in Management
   - Add Meta Quest feature group

5. Open `Assets/SollertiaDemo.unity` and press Play

For Quest 3 deployment, see [`QUEST_DEPLOYMENT.md`](game/Assets/Scripts/Sollertia/QUEST_DEPLOYMENT.md).

### Hardware Setup

1. Connect FSR sensors to Arduino:

   ```
   FSR 1 (Index)  → A0
   FSR 2 (Middle) → A1
   ```

2. Upload firmware:

   ```bash
   # Open hardware/hardware.ino in Arduino IDE
   # Select board: Arduino Uno
   # Upload
   ```

3. Verify serial output at 9600 baud

### Dashboard Setup

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
|-- game/                          # Unity XR application
|   |-- Assets/
|   |   |-- Scripts/Sollertia/
|   |   |   |-- SollertiaDemo.cs   # Main application logic
|   |   |   |-- SETUP_INSTRUCTIONS.md
|   |   |   |-- QUEST_DEPLOYMENT.md
|   |   |-- Settings/              # XR plugin configuration
|   |   |-- SollertiaDemo.unity    # Main scene
|   |-- Packages/
|   |-- ProjectSettings/
|
|-- dashboard/                     # Rust clinical dashboard
|   |-- Cargo.toml
|   |-- crates/
|
|-- hardware/                      # Arduino sensor firmware
|   |-- hardware.ino
|
|-- paper/                         # Research whitepaper (LaTeX)
|   |-- main.tex
|   |-- references.bib
|   |-- figures/
|
|-- docs/                          # GitHub Pages site
|   |-- index.html
|
|-- README.md
|-- LICENSE
|-- AGENTS.md
```

---

## Whitepaper

The research whitepaper is available at: **[sollertia.github.io](https://anaya33.github.io/Sollertia/)**

The paper details:

- Theoretical background on motor control assessment
- System design and implementation
- Validation methodology
- Preliminary findings
- Future research directions

---

## Contributing

Contributions are welcome. Areas of interest:

| Area | Description |
|------|-------------|
| **Signal Processing** | Improved force signal filtering and feature extraction |
| **Machine Learning** | Motor pattern classification and anomaly detection |
| **Clinical Validation** | Study design and IRB protocol development |
| **Hardware** | Additional sensor modalities (IMU, EMG) |
| **Visualization** | Enhanced dashboard and analysis tools |

### Contribution Guidelines

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Commit changes with clear messages
4. Submit a pull request with description of changes

---

## References

### Primary Sources

1. Lu, X., et al. (2019). *Modeling Endpoint Distribution of Pointing Selection Tasks in Virtual Reality Environments.* [PDF](https://xueshilu.github.io/papers/Modeling%20Endpoint%20Distribution%20of%20Pointing%20Selection%20Tasks%20in%20Virtual%20Reality%20Environments.pdf)

2. Chen, D., et al. (2024). *Metrics of Motor Learning for Analyzing Movement Mapping in Virtual Reality.* [PDF](https://www.difeng.me/papers/24_Metrics.pdf)

3. Schoen, A., et al. (2025). *From Pegs to Pixels: A Comparative Analysis of the Nine Hole Peg Test and a Digital Copy Drawing Test for Fine Motor Control Assessment.* [PDF](https://thomaskosch.com/wp-content/papercite-data/pdf/schoen2025from.pdf)

4. Wei, X., et al. (2019). *Accurate and Low-Latency Sensing of Touch Contact on Any Surface with Finger-Worn IMU Sensor.* [PDF](https://xiaoying-wei.github.io/TappingRing.pdf)

5. Schneider, O., et al. (2021). *Accuracy Evaluation of Touch Tasks in Commodity Virtual and Augmented Reality Head-Mounted Displays.* [PDF](https://pokristensson.com/pubs/SchneiderEtAlSUI2021.pdf)

### Supporting Literature

6. Yorke, A., et al. (2024). *A Machine Learning Approach for Predicting Upper Limb Motion Intentions with Multimodal Data in Virtual Reality.* arXiv:2405.13023. [Link](https://arxiv.org/abs/2405.13023)

7. Fitts, P. M. (1954). *The information capacity of the human motor system in controlling the amplitude of movement.* Journal of Experimental Psychology, 47(6), 381-391.

8. Laver, K. E., et al. (2017). *Virtual reality for stroke rehabilitation.* Cochrane Database of Systematic Reviews.

---

## Team

| Name | Role | Contact |
|------|------|---------|
| **Anaya Yorke** | Lead Researcher | [@anaya33](https://github.com/anaya33) |
| **David Salas C.** | Hardware Development | |
| **Garret S. Stand** | Software Engineering | [@gstand](https://github.com/gstand) |
| **Mathias Sosa** | XR Development | |

*Originally developed at UGAHacks XI, now continued as an independent research project.*

---

## License

This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE) file for details.

---

<div align="center">

**Sollertia** - *Latin for "skill with hand"*

[View Whitepaper](https://anaya33.github.io/Sollertia/) | [Report Issues](https://github.com/anaya33/Sollertia/issues)

</div>
