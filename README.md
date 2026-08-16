# 🗺️ Audio-Tactile Map (ATM) for the Gannett Art Gallery

<div align="center">

![FinalMap](docs/images/rex_hero.png)\

**An Accessible, Modular Assistive Technology Exhibit for Visually Impaired Visitors**

[![Status](https://img.shields.io/badge/status-deployed-success)](https://gannettgallery.org)
[![Hardware](https://img.shields.io/badge/hardware-3D%20Printed%20PLA-blue)]()
[![Electronics](https://img.shields.io/badge/electronics-Arduino%20MEGA%202560-orange)]()
[![License](https://img.shields.io/badge/license-MIT-lightgrey)](LICENSE)



**A Capstone Project by Bao Do, Cong Du Pham, Edric Pham — SUNY Polytechnic Institute**  
*In collaboration with the CCAVA Initiative & CABVI*

</div>

---

## 📋 Table of Contents

- [Overview](#overview)
- [Motivation & Impact](#motivation--impact)
- [System Architecture](#system-architecture)
- [Mechanical Design](#mechanical-design)
- [Electrical & Firmware](#electrical--firmware)
- [Software & Audio Pipeline](#software--audio-pipeline)
- [Manufacturing & Assembly](#manufacturing--assembly)
- [Validation & Testing](#validation--testing)
- [User Evaluation](#user-evaluation)
- [Bill of Materials](#bill-of-materials)
- [Team & Contributions](#team--contributions)
- [Future Roadmap](#future-roadmap)
- [Publications & Acknowledgments](#publications--acknowledgments)

---

## Overview

The **Audio-Tactile Map (ATM)** is a modular, 3D-printed assistive device designed for the **Gannett Art Gallery** at SUNY Polytechnic Institute. It enables visitors with visual impairments to independently navigate gallery exhibitions through a combination of **tactile braille feedback** and **spatial audio narration**.

The system features a 32-point interactive surface where each tactile button corresponds to an artwork or gallery landmark. Pressing a button triggers a pre-recorded audio description stored on a local SD card, delivered through either a built-in speaker or headphones. The entire enclosure is engineered for durability, modularity, and ease of maintenance.

> **Key Achievement:** The ATM sustained **>2,500 lb compressive load** and **>9,900 lb tensile load** in destructive testing, achieving a **safety factor of 15** under operational loads.

---

## Motivation & Impact

The [Creating Collaboratively Across Visual Abilities (CCAVA)](https://gannettgallery.org) project, funded by a SUNY Innovative Instructional Grant, addresses the critical need for accessible museum and gallery experiences. While digital accessibility tools exist, they often:

- Require personal smart devices (creating economic barriers)
- Disrupt spatial memory formation through audio overload
- Lack tactile feedback for cognitive mapping

The ATM solves these by providing a **standalone, haptic-first interface** with optional audio augmentation. It was developed in continuous consultation with the **Central Association for Blind and Visually Impaired (CABVI)**, ensuring real-world usability.

<!-- TODO: Add CCAVA poster / team photo here -->
<!-- ![CCAVA Team](docs/images/ccava_team.jpg) -->

---

## System Architecture

The ATM is organized as a **three-layer modular stack**, with each layer segmented into four quadrants to accommodate standard FDM printer build volumes (220×220 mm).

```
┌─────────────────────────────────────────┐
│  TOP LAYER                              │
│  • Tactile gallery contour map          │
│  • 32 removable Braille blocks          │
│  • Interchangeable title plate slot     │
├─────────────────────────────────────────┤
│  MIDDLE LAYER                           │
│  • 32 momentary push buttons            │
│  • Braille block seating & retention    │
│  • M3 bolt tabs for layer clamping      │
├─────────────────────────────────────────┤
│  BOTTOM LAYER                           │
│  • Arduino MEGA 2560                    │
│  • Micro SD card reader (audio storage) │
│  • LM386 audio amplifier                │
│  • Speaker / 3.5mm headphone jack       │
│  • Power distribution & battery holder  │
│  • Structural pillar supports           │
└─────────────────────────────────────────┘
```

### Specifications

| Parameter | Value |
|-----------|-------|
| **Overall Dimensions** | 12" × 12" × 3.5" (L×W×H) |
| **Material** | Polylactic Acid (PLA) |
| **Layer Thickness** | 0.5" (base plates) |
| **Fasteners** | M3×0.5 Steel Socket-Head Cap Screws |
| **Input Voltage** | 5V DC (USB / Battery pack) |
| **Audio Output** | 8Ω Speaker + 3.5mm Stereo Jack |
| **Audio Format** | 8-bit PCM, 16 kHz, Mono, `.wav` |
| **I/O Capacity** | 32 digital inputs |

<!-- TODO: Add exploded CAD view here -->
<!-- ![Exploded View](docs/images/exploded_view.png) -->

---

## Mechanical Design

### CAD & Modeling
All mechanical components were designed in **SolidWorks 2024** with a design-for-manufacturing (DFM) approach optimized for FDM 3D printing.

#### Top Baseplate
- **Gallery Contour:** Raised 0.15" ridges trace the physical layout of the Gannett Gallery
- **Braille Blocks:** 32 numbered, removable blocks (0.49" × 0.42" × 0.65") with standard NAB-scale Braille domes
- **Title Plate Slot:** A sliding Braille plate (12" × 0.9" × 0.1") secured with a screw-cap for exhibition-specific labeling
- **Screw Tabs:** Integrated M3 clearance holes (Ø0.08") for layer clamping

![Top Plate Drawing](docs/images/top_plate_drawing.png)

#### Middle Layer (Button Support)
- **Button Seats:** 32 square cutouts (0.67" × 0.5") with 0.15" retention walls
- **Blanking Capability:** Unused positions accept flush blanking plates to prevent accidental actuation
- **Alignment Features:** Self-locating pegs ensure quadrant registration during assembly

![Middle Layer](docs/images/middle_layer.jpg)

#### Bottom Layer (Electronics Chassis)
- **Component Mounts:** Dedicated bosses for Arduino MEGA, SD reader, solderable breadboard, battery holder, and speaker
- **Pillar Supports:** Four reinforced pillars (2.0" height) with slip-on brackets distribute compressive loads
- **Access Ports:** Side-panel cutouts for power switch, SD card access, USB programming, and audio jack

![Bottom Layer](docs/images/bottom_layer.jpg)

### Structural Analysis (ANSYS)

Finite Element Analysis was performed on the bottom load-bearing plate using **ANSYS APDL** and **ANSYS Workbench**.

| Analysis | Result | Safety Factor |
|----------|--------|---------------|
| Max Deflection (Center) | 1.17×10⁻⁴ in | — |
| Bending Stress (Center) | 0.504 psi | — |
| Von Mises Stress | 0.589 psi | **15** |

**Material Properties (PLA):**
- Density: 0.047 lb/in³
- Young's Modulus: 340 ksi
- Poisson's Ratio: 0.30
- Tensile Yield: 6.6 ksi
- Tensile Ultimate: 8.31 ksi

<!-- TODO: Add ANSYS stress plots here -->
<!-- ![ANSYS Von Mises](docs/images/ansys_von_mises.png) -->
<!-- ![ANSYS Safety Factor](docs/images/ansys_safety_factor.png) -->

---

## Electrical & Firmware

### Wiring Architecture

The electrical system was refined from a prototype to minimize failure points and simplify maintenance.

#### Button Matrix (32×1)
All 32 buttons use **INPUT_PULLUP** mode on the Arduino MEGA. One terminal of each button connects to a unique digital pin; the opposing terminals are bussed together on a **solderable breadboard** acting as a centralized ground plane.

| Button Group | Arduino Pins |
|--------------|--------------|
| 1 – 8 | D2 – D9 |
| 9 – 32 | D22 – D45 |

<!-- TODO: Add button wiring diagram here -->
<!-- ![Button Wiring](docs/images/button_wiring.png) -->

#### Micro SD Card Reader (SPI)
| SD Module | Arduino MEGA |
|-----------|--------------|
| VCC | 5V |
| GND | GND |
| MOSI | D51 |
| MISO | D50 |
| SCK | D52 |
| CS | D53 |

![SD Card Wiring](docs/images/sd_card_wiring.png)

#### Audio Output Chain
```
Arduino D11 (PWM) 
    → 3-Pin Toggle Switch (Headphone / Speaker)
    → LM386 Audio Amplifier Module
    → Output: Speaker or 3.5mm TRS Breakout (L/R/GND)
```

![Audio Amplifier](docs/images/audio_amp.jpg) 

### Firmware

The firmware is built on the **TMRpcm** library for PCM audio playback from SD card.

```cpp
#include "SD.h"
#include "TMRpcm.h"
#include "SPI.h"

#define SD_ChipSelectPin 53
TMRpcm tmrpcm;

void setup() {
  tmrpcm.speakerPin = 11;
  if (!SD.begin(SD_ChipSelectPin)) return;
  tmrpcm.setVolume(5);

  // Configure all 32 button pins
  for (int pin = 2; pin <= 45; pin++) {
    if (pin == 10 || pin == 11 || pin == 12 || pin == 13) continue; // Skip SPI
    pinMode(pin, INPUT_PULLUP);
  }
}

void loop() {
  if (digitalRead(BUTTON_PIN2) == LOW) {
    tmrpcm.pause();
    tmrpcm.play("a.wav");   // Artwork slot 'a'
  }
  // ... 31 additional if-clauses for b.wav through ff.wav
}
```

**Key Design Decisions:**
- **Non-blocking audio:** `pause()` prevents clip overlap
- **Direct pin mapping:** Avoids matrix scanning latency for immediate tactile response
- **Ground bussing:** Eliminates 32 individual ground wires, reducing assembly time by ~40%

<!-- TODO: Add Arduino code screenshot / IDE photo here -->
<!-- ![Arduino Code](docs/images/arduino_code.png) -->

---

## Software & Audio Pipeline

### Audio File Preparation

All audio tracks must be converted to a specific PCM format for TMRpcm compatibility:

```bash
# Using ffmpeg
ffmpeg -i input.mp3 -ar 16000 -ac 1 -c:a pcm_u8 output.wav
```

**Required Format:**
- Bit resolution: 8-bit
- Sampling rate: 16,000 Hz
- Channels: Mono
- PCM format: Unsigned 8-bit (U8)
- Extension: `.wav`

### File Naming Convention

Tracks are mapped to gallery positions using a single-letter naming scheme (`a.wav` through `ff.wav`), corresponding to the physical Braille block numbering system arranged clockwise from the gallery entrance.

<!-- TODO: Add audio file naming diagram / SD card contents screenshot here -->
<!-- ![Audio File Names](docs/images/audio_file_names.jpg) -->

---

## Manufacturing & Assembly

### 3D Printing
- **Printer:** Creality Ender 3 S1 Pro
- **Material:** MatterHackers PLA / Polymaker Polysmooth PLA
- **Print Time:** ~4 hours per quadrant (12 unique parts total)
- **Infill:** 20% (structural), 40% (pillar brackets)
- **Layer Height:** 0.2 mm

<!-- TODO: Add 3D printing in-progress photo here -->
<!-- ![3D Printing](docs/images/3d_printing.jpg) -->

### Assembly Procedure

1. **Bottom Layer Assembly**
   - Join four quadrant shells using alignment pegs
   - Slide pillar brackets onto support posts
   - Drill 2.5 mm pilot holes and secure with M3 bolts
   - Mount Arduino, SD reader, breadboard, and battery holder

<!-- TODO: Add bottom layer assembly photo here -->
<!-- ![Bottom Assembly](docs/images/bottom_assembly.jpg) -->

2. **Electronics Integration**
   - Solder button ground legs to the shared bus board
   - Route signal wires directly to Arduino headers
   - Connect audio amplifier and output switch
   - Verify continuity with multimeter

<!-- TODO: Add wiring harness photo here -->
<!-- ![Wiring Harness](docs/images/wiring_harness.jpg) -->

3. **Middle & Top Layer Assembly**
   - Install 32 push buttons into middle-layer seats
   - Join top plate quadrants and align over button stems
   - Insert Braille blocks into numbered positions
   - Clamp all three layers using perimeter M3 bolts

<!-- TODO: Add top layer assembly photo here -->
<!-- ![Top Assembly](docs/images/top_assembly.jpg) -->

4. **Quality Check**
   - Actuation force per button: **< 1 lb**
   - Audio clarity test on both output channels
   - SD card hot-swap verification

---

## Validation & Testing

### Mechanical Testing (Instron 33R4206)

| Test | Theoretical Limit | Experimental Result |
|------|-------------------|---------------------|
| **Tensile** | 9,972 lbf (44.3 kN) | Specimen yielded at high load; no structural failure under operational loads |
| **Compressive** | 3,324 lbf (14.8 kN) | Deformation initiated at **2,500 lbf** |

> **Conclusion:** The ATM withstands >1000× its operational load (electronics + user interaction ≈ 1–2 lbf), confirming robustness for public deployment.

<!-- TODO: Add Instron testing photos here -->
<!-- ![Tensile Test](docs/images/tensile_test.jpg) -->
<!-- ![Compressive Test](docs/images/compressive_test.jpg) -->

### Electrical Testing
- **Button bounce:** < 5 ms (measured on oscilloscope)
- **Audio latency:** < 50 ms from press to playback
- **Current draw:** 180 mA @ 5V (typical), 320 mA peak (SD read + audio amp)

---

## User Evaluation

On **April 25, 2025**, representatives from **CABVI** evaluated the ATM in a live gallery setting alongside a refreshable Braille tablet companion device.

<!-- TODO: Add CABVI testing / gallery installation photo here -->
<!-- ![Gallery Installation](docs/images/gallery_installation.jpg) -->

### Positive Feedback
- Intuitive tactile navigation with Braille block spacing
- Clear audio quality through both speaker and headphones
- Modular braille plate allows easy exhibition updates

### Actionable Improvements (v3.0 Roadmap)
| Issue | Proposed Solution |
|-------|-------------------|
| Speaker located at rear | Relocate to front-facing grille |
| No power-on indicator | Add LED or startup audio chime |
| Fixed audio speed | Implement potentiometer or digital speed control |
| No volume separation | Independent volume trim for speaker vs. headphones |
| Missing numeric Braille prefix | Add "⠼" (number indicator) before each Braille numeral |

---

## Bill of Materials

### Electronics
| Part | Qty | Est. Cost |
|------|-----|-----------|
| Arduino MEGA 2560 | 1 | $48.40 |
| Micro SD Card Reader Module | 1 | $6.99 |
| LM386 Audio Amplifier Module | 1 | $3.50 |
| 3-Pin 3-Position Toggle Switch | 1 | $6.99 |
| 3.5mm Stereo Jack Breakout | 1 | $11.73 |
| 8Ω Mini Speaker | 1 | $10.19 |
| Tactile Push Buttons (12×12 mm) | 32 | $8.00 |
| Solderable Breadboard | 1 | $7.99 |
| M3×8 mm Socket Head Bolts | 50 | $5.00 |

### Mechanical
| Part | Qty | Est. Cost |
|------|-----|-----------|
| PLA Filament (1 kg, Black) | 2 rolls | $41.78 |
| PLA Filament (White, for Braille) | 1 roll | $20.89 |

**Total Estimated BOM:** ~$170 USD

---

## Team & Contributions

| Member | Focus Area | Key Contributions |
|--------|------------|-----------------|
| **Bao Do** | Electrical & Firmware | Arduino architecture, audio pipeline, wiring harness, soldering, user testing coordination |
| **Edric Pham** | Top & Middle Layer CAD | Braille block design, top plate contouring, modularity framework, drafting |
| **Cong Du Phan** | Bottom Layer & FEA | Structural analysis, ANSYS simulation, pillar support design, bottom chassis layout |

**Project Advisor:** Dr. D.K. Jones  
**Partner Organization:** Central Association for Blind and Visually Impaired (CABVI)

<!-- TODO: Add team photo here -->
<!-- ![Team Photo](docs/images/team_photo.jpg) -->

---

## Future Roadmap

- [ ] **v2.1:** Front-facing speaker grille and power LED
- [ ] **v2.2:** Adjustable playback speed (0.5×–1.5×) via rotary encoder
- [ ] **v3.0:** Battery level monitoring and USB-C PD power delivery
- [ ] **v3.5:** ESP32-S3 wireless variant for Over-The-Air (OTA) audio updates
- [ ] **v4.0:** Haptic vibration feedback layer for deaf-blind accessibility

---

## Publications & Acknowledgments

This project was developed as part of the **MTC 424/426 Capstone Experience** at SUNY Polytechnic Institute under the **CCAVA** initiative.

### Acknowledgments
- **SUNY Academic Innovation Grants Program** — Funding
- **SUNY Poly Center for Global Advanced Manufacturing (CGAM)** — 3D printing resources
- **CABVI** — User testing and design consultation
- **Dr. D.K. Jones** — Academic advising and project oversight

### Related Work
- Griffin, E., Picinali, L., & Scase, M. (2020). *The effectiveness of an interactive audio-tactile map for the process of cognitive mapping and recall among people with visual impairments.*
- Kaplan, H. & Pyayt, A. (2023). *Fully Digital Audio Haptic Maps for Individuals with Blindness.*
- Holmes, K. (2018). *Mismatch: How Inclusion Shapes Design.* MIT Press.

---

<div align="center">

**[⬆ Back to Top](#-audio-tactile-map-atm-for-the-gannett-art-gallery)**

*Built with accessibility in mind. Open to collaboration.*

</div>
