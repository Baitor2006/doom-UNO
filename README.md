# ⚡ DOOM_UNO: 3D Raycasting Engine on an 8-Bit Microcontroller

## 📖 Overview
**DOOM_UNO** is a real-time 3D rendering engine built for the Arduino Uno (ATmega328P). This project adapts the architectural concepts of the legendary 1993 game *DOOM* to run on a restricted 8-bit architecture with only **2 KB of SRAM** and **32 KB of Flash memory**. 

This project is built upon the open-source framework of [doom-nano](https://github.com/adeas31/doom-nano), heavily refactored and customized to interface with our custom hand-crafted physical hardware shield.

**Project Motto:** *"The impossible."*

---

## 🛠️ Hardware & "Sandwich" Architecture
The project is built using a two-layer vertical sandwich design, mounting a custom-etched application shield directly onto the Arduino Uno headers.

* **Microcontroller:** Arduino Uno R3 (ATmega328P @ 16MHz).
* **Display:** 0.96" Monochrome I2C OLED display running an optimized low-level driver.
* **Control Layout:** 5 yellow tactile push-buttons arranged in a custom asymmetrical cluster configuration (3 navigation buttons + 2 action/fire buttons) utilizing internal pull-up resistors (`INPUT_PULLUP`).
* **Audio:** Direct PWM-driven Piezo buzzer rendering constant-time sound effects.
* **Chassis & Power:** Mounted on a translucent blue acrylic baseplate powered by a 9V VARTA cell.

---

## 🚀 Engineering Optimizations vs. Base Framework
To port the core raycasting loop smoothly to our hardware without crashing the microcontroller, several low-level software modifications were implemented over the base `doom-nano` code:

1. **Native SH1106/SSD1306 Display Driver:** Replaced heavy generic display libraries with a lightweight, direct `Wire.h`-based implementation (`SH1106.h`). This layout bypasses dynamic heap allocation, saving crucial bytes of static SRAM.
2. **Precision Reduction (Double to Float):** Modified complex trigonometric math and multi-coordinate transformation equations from 64-bit `double` functions to 32-bit `float` operations. On 8-bit AVR microcontrollers lacking an FPU, this adjustment prevented heavy software emulation, speeding up frame rates and saving over 300 bytes of ROM.
3. **PROGMEM Matrix Storage:** Relocated all fixed game assets—including map levels (`level.h`), sound patterns, custom word-fonts (`splash.h`), and bitmaps—directly into Flash memory (`PROGMEM`) to keep the 2 KB SRAM safe from runtime overflow.
4. **ISR Sound Optimization:** Optimized the audio interrupt service routine from a sequential 34-branch conditional evaluation block down to a constant-time $O(1)$ lookup table inside Flash memory, eliminating processor stutter when firing weapons during deep rendering passes.

---

## 📂 Codebase Structure
* `doom-nano.ino` — Master orchestration file managing the main execution loop, hardware initialization, and scene states.
* `constants.h` — Central configuration mapping custom physical I/O pin assignments, engine frame times, and geometric constraints.
* `SH1106.h` — Custom, low-overhead monochrome display driver.
* `display.h` — Viewport rendering helpers, fast vertical line engines (`drawVLine`), and raycasting projection support.
* `input.h` / `input.cpp` — Low-level interface mapping physical button inputs to game actions.
* `entities.h` / `entities.cpp` — Allocation-free structural definition arrays handling static and active map elements (enemies, keys, medikits).
* `map.h` — Renders the pre-game level layout and blink-tracking player coordinate marker onto the display viewport.
* `sound.h` — Timer-driven tone generators playing audio arrays out of code memory.
* `level.h` / `sprites.h` / `splash.h` — Fixed structural layout holding map configurations, custom font textures, and splash assets in Flash.

---

## 🎓 Project Context & Credits
This project was developed as a Final Module Project (*Module : Travaux de Réalisation II*) at **ISTA INTER-ENTREPRISES CASABLANCA**.

* **Filière:** Automatisation et Instrumentation Industrielle (AII 1A)
* **Group:** AII 104
* **Developer:** Omar Alaoui Belghiti
* **Project Supervisor:** M. Essaiydy

*Special thanks to our instructor, **M. Essaiydy**, for providing the raw copper boards, precision soldering tools, and expert hardware design guidance required to turn our schematics into a fully functional physical prototype.*
