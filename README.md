<div align="center">

# Audio-Tactile Map for the Gannett Art Gallery

![Final audio-tactile map installation](images/finalmap.png)

### A modular, tactile-first interface for independent gallery navigation

[![Deployed](https://img.shields.io/badge/Deployed-Gannett%20Gallery-success)](#deployment)
[![Community feedback](https://img.shields.io/badge/CABVI-Formative%20Evaluation-blue)](#validation)
[![Mechanical validation](https://img.shields.io/badge/ANSYS%20Safety%20Factor-15-critical)](#mechanical-validation)
[![Unit cost](https://img.shields.io/badge/Unit%20Cost-%24170-orange)](#bill-of-materials)

</div>

---

## Overview

The Audio-Tactile Map is a standalone assistive interface that helps blind and low-vision visitors build a spatial understanding of the Gannett Art Gallery before exploring it.

A visitor explores a tactile gallery map, presses one of 32 Braille-labeled buttons, and hears a location-specific audio description of the corresponding artwork or gallery area. The device operates without a smartphone, internet connection, or sighted assistance.

The system was developed through iterative consultation with the Central Association for the Blind and Visually Impaired (CABVI) and is deployed at the Gannett Art Gallery at SUNY Polytechnic Institute.

| | |
|:---|:---|
| **Function** | Tactile gallery map with 32 location-aware audio descriptions |
| **Primary users** | Blind and low-vision gallery visitors |
| **Deployment site** | [Gannett Art Gallery](https://gannettgallery.org), SUNY Polytechnic Institute |
| **Community partner** | [CABVI](https://www.cabvi.org) |
| **Embedded platform** | Arduino Mega 2560, TMRpcm, SPI SD-card storage |
| **Audio format** | 8-bit PCM, 16 kHz mono WAV |
| **Manufacturing** | FDM 3D printing in PLA |
| **Unit cost** | Approximately $170 |

---

## Contents

- [How it works](#how-it-works)
- [Design motivation](#design-motivation)
- [System architecture](#system-architecture)
- [Validation](#validation)
- [Build it](#build-it)
- [Team and contributions](#team-and-contributions)
- [Acknowledgments](#acknowledgments)

---

## How it works

```text
Touch
  │
  ▼
Braille-labeled button pressed
  │
  ▼
Arduino Mega reads button state
  │
  ▼
Corresponding WAV file loaded from SD card
  │
  ▼
Audio description played through speaker or headphones
```

Each tactile button is mapped to a gallery location. Pressing a button pauses any active playback and starts the corresponding audio description.

The audio content is stored on a removable SD card, so exhibition descriptions can be updated without modifying or reflashing the firmware.

---

## Design motivation

Museums are often primarily visual environments. Existing accessibility tools can introduce additional barriers:

- Smartphone-based guides may require personal devices, digital literacy, or reliable connectivity.
- Unstructured audio guides can be difficult to relate to physical space.
- Without a tactile reference frame, visitors may have difficulty forming a mental model of the gallery layout.

The Audio-Tactile Map addresses these issues by combining a physical tactile map with location-specific audio descriptions. Visitors can explore the gallery layout through touch, associate locations with audio descriptions, and navigate more independently.

The design was refined through iterative consultation with CABVI. This collaboration informed the tactile layout, Braille-block design, contrast choices, audio quality, and usability of the final installation.

---

## System architecture

The device uses a three-layer modular assembly. Each layer is divided into four quadrants so that all components fit within a standard 220 × 220 mm FDM build volume.

![Exploded view of the three-layer modular CAD assembly](images/explodedview.png)

| Layer | Function | Key features |
|---|---|---|
| **Top** | Tactile interface | Gallery contour map, 32 removable Braille blocks, interchangeable exhibition title plate |
| **Middle** | Switch support | 32 momentary tactile switches, blanking plates, M3 alignment tabs |
| **Bottom** | Electronics chassis | Arduino Mega 2560, SPI SD-card reader, LM386 amplifier, speaker/headphone output, battery compartment, structural pillars |

**Materials:** PLA structural components and M3 socket-head fasteners.  
**Overall dimensions:** 305 × 305 × 89 mm (12 × 12 × 3.5 in).  
**Audio encoding:** 8-bit unsigned PCM, 16 kHz sample rate, mono, WAV container.

### Top plate

![Top plate showing tactile gallery topology](images/topplate.png)

The top plate provides the tactile gallery topology and holds removable Braille blocks corresponding to gallery locations.

### Middle plate

![Middle plate showing switch mounting and alignment features](images/middleplate.png)

The middle plate mounts the 32 tactile switches and aligns the three structural layers.

### Bottom plate

![Bottom plate showing electronics integration and structural load path](images/bottomplate.png)

The bottom plate houses the electronics, audio-output components, battery compartment, and structural load path.

---

## Validation

### Community feedback

The installation was evaluated with CABVI as part of a formative design process. Feedback addressed tactile discriminability, audio intelligibility, and usability for gallery navigation.

> “The modular Braille blocks and clear audio quality make independent navigation possible. Moving the speaker to the front and adding adjustable audio speed would make this even stronger.”  
> — CABVI formative evaluation feedback, April 25, 2025

This feedback informed continued refinement of the installation. The system is deployed for rotating exhibitions and remains open to iterative improvement.

### Mechanical validation

<details>
<summary><strong>Structural analysis and destructive testing</strong></summary>

The bottom load-bearing plate was analyzed using ANSYS APDL and ANSYS Workbench finite-element methods.

| Metric | Result |
|---|---:|
| Maximum center deflection | 1.17 × 10⁻⁴ in |
| Maximum bending stress at center | 0.504 psi |
| Maximum Von Mises stress | 0.589 psi |
| Safety factor | 15 |

Destructive testing was performed using an Instron 33R4206 universal testing machine.

| Test | Result |
|---|---:|
| Tensile loading | Failure above 9,900 lbf |
| Compressive loading | Plastic deformation initiated at 2,500 lbf |

![ANSYS factor-of-safety distribution](images/ansyssafety.png)

![Von Mises stress contour plot](images/ansysvonmises.png)

</details>

### Electrical architecture

<details>
<summary><strong>32-channel direct-mapped input architecture</strong></summary>

All 32 tactile buttons are connected directly to Arduino digital inputs configured with internal pull-up resistors. A centralized solderable breadboard provides a shared ground bus, reducing the number of individual ground-return wires and simplifying assembly.

The audio signal path is:

```text
Arduino PWM output, pin D11
  → 3-position output-mode toggle
  → LM386 audio amplifier
  → 8-ohm speaker or 3.5 mm TRS headphone jack
```

![Electrical system wiring and component layout](images/electrical.png)

</details>

### Firmware

<details>
<summary><strong>TMRpcm embedded audio engine</strong></summary>

The firmware uses the TMRpcm library to play WAV files from an SPI-connected SD card.

```cpp
#include "SD.h"
#include "TMRpcm.h"

#define SD_CS 53

TMRpcm tmrpcm;

void setup() {
  tmrpcm.speakerPin = 11;
  SD.begin(SD_CS);
  tmrpcm.setVolume(5);

  // Configure 32 button pins as INPUT_PULLUP.
}

void loop() {
  if (digitalRead(BUTTON_PIN_2) == LOW) {
    tmrpcm.pause();
    tmrpcm.play("a.wav");
  }

  // Additional button-to-audio mappings.
}
```

Key implementation details:

- `pause()` is called before starting a new track to prevent overlapping playback.
- Tactile-to-audio response is below 50 ms.
- Audio descriptions can be replaced by swapping the SD card; no firmware reflashing is required.

</details>

### Audio pipeline

<details>
<summary><strong>PCM encoding and content management</strong></summary>

Audio files are batch-converted to a TMRpcm-compatible format using FFmpeg:

```bash
ffmpeg -i input.mp3 -ar 16000 -ac 1 -c:a pcm_u8 output.wav
```

| Parameter | Requirement |
|---|---|
| Bit depth | 8-bit unsigned |
| Sample rate | 16,000 Hz |
| Channels | Mono |
| Container | WAV |

Files are named `a.wav` through `ff.wav` and mapped to the Braille block positions on the gallery topology.

</details>

---

## Build it

### Bill of materials

| Category | Part | Quantity | Approximate cost |
|---|---|---:|---:|
| Controller | Arduino Mega 2560 | 1 | $48 |
| Storage | MicroSD-card reader, SPI | 1 | $7 |
| Audio | LM386 amplifier module | 1 | $3.50 |
| Audio | 3.5 mm TRS breakout and toggle switch | 1 | $19 |
| Audio | 8-ohm speaker | 1 | $10 |
| Input | 12 × 12 mm tactile push buttons | 32 | $8 |
| Power | Solderable breadboard and wiring | 1 | $8 |
| Structure | PLA filament, black | 2 kg | $42 |
| Structure | PLA filament, white | 1 kg | $21 |
| Hardware | M3 × 8 mm socket-head bolts | 50 | $5 |
| **Total** | | | **Approximately $170** |

### Assembly procedure

1. Fabricate 12 quadrant components—four per layer—using an FDM printer with at least a 220 × 220 mm build volume.
2. Wire tactile buttons to the shared ground bus and route individual signal lines to Arduino digital pins D2–D45.
3. Integrate the SPI SD-card reader, LM386 amplifier, D11 PWM audio output, and power-management switch.
4. Assemble the layers using M3 fasteners, insert Braille blocks, and load exhibition audio onto the SD card.
5. Validate every button for reliable actuation and clean, non-distorted audio playback.

---

## Team and contributions

| Contributor | Contribution |
|---|---|
| **Bao Do** | Electrical architecture, embedded firmware development, wiring integration, system testing, and CABVI liaison |
| **Edric Pham** | Top- and middle-layer CAD modeling, Braille-block geometry, and technical drafting |
| **Cong Du Phan** | Bottom-layer structural CAD, ANSYS finite-element analysis, mechanical validation, and destructive testing |
| **Faculty advisor** | Dr. D.K. Jones |
| **Community partner** | [CABVI](https://www.cabvi.org) — user-centered design consultation, formative evaluation, and accessibility expertise |

---

## Acknowledgments

- SUNY Academic Innovation Grants Program for project funding.
- SUNY Poly CGAM for additive-manufacturing resources and fabrication support.
- CCAVA Initiative for the project framework and institutional commitment to accessibility in cultural heritage.

---

<div align="center">

**[Back to top](#audio-tactile-map-for-the-gannett-art-gallery)**

Designed for accessibility. Open to collaboration and replication.

</div>
