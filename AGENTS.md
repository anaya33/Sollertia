# AGENTS.md

This file provides guidance to AI coding assistants when working with code in this repository.

## Project Overview

Sollertia is a research system for quantifying fine motor control in extended reality (XR) environments. Licensed under GPLv3. Originally developed at UGAHacks XI, now continued as an independent research project.

The system measures fine motor control through task-based interaction and wearable force sensing. It replicates a standardized button-pressing task in both physical and XR conditions, combining behavioral metrics (reaction time, accuracy, trajectory) with continuous force data from finger-mounted FSR sensors.

**Research Focus:**
- Validating XR-based motor assessments against physical equivalents
- Characterizing fine motor control through multi-modal measurement
- Supporting rehabilitation research for neurological conditions (e.g., stroke)

## Architecture

The project is organized into four components:

- **game/** - Unity XR application (Meta Quest 3, hand tracking)
- **dashboard/** - Rust-based clinical dashboard (real-time visualization, session management)
- **hardware/** - Arduino firmware for FSR sensors
- **paper/** - LaTeX whitepaper and research documentation
- **docs/** - GitHub Pages site for whitepaper deployment

## Build Commands

### Unity (game/)
```bash
# Open in Unity 6 (6000.3.7f1+)
# Build via Unity Editor or command line:
/Applications/Unity/Hub/Editor/[VERSION]/Unity.app/Contents/MacOS/Unity -batchmode -projectPath ./game -buildTarget Android -quit
```

### Rust (dashboard/)
```bash
cd dashboard
cargo build              # Debug build
cargo build --release    # Release build
cargo test               # Run tests
cargo clippy             # Lint
cargo fmt                # Format code
```

### Arduino (hardware/)
```bash
# Open hardware/hardware.ino in Arduino IDE
# Select board: Arduino Uno
# Upload to device
```

### LaTeX (paper/)
```bash
cd paper
pdflatex main.tex
bibtex main
pdflatex main.tex
pdflatex main.tex
```

## Data Flow

```
Meta Quest 3 (XR App) --WebSocket--> Dashboard (Rust)
Arduino (FSR Sensors) --USB Serial--> Dashboard (Rust)
```

## Key Files

- `game/Assets/Scripts/Sollertia/SollertiaDemo.cs` - Main XR application logic
- `dashboard/crates/` - Rust dashboard modules
- `hardware/hardware.ino` - FSR sensor firmware
- `paper/main.tex` - Research whitepaper
- `docs/index.html` - GitHub Pages site

## Metrics Collected

**Behavioral:** reaction time, movement time, spatial error, trajectory, variability
**Force:** peak force, onset time, duration, variability, force profile

## Cross-Component Communication

- XR App → Dashboard: WebSocket (JSON events)
- Hardware → Dashboard: USB Serial (binary protocol, 9600 baud)
- Timestamps synchronized across all streams
