# ⚡ Kururugi - Electronic Speed Controller (ESC) ⚡ 

> 4/6S-ready • 64–96 kHz PWM • FOC/Six-Steps • Full bare-metal control

<p align="center">
  <img src="./docs/exports/imgs/Kururugi-V0.1-Front.jpg" width=40%" />
  <img src="./docs/exports/imgs/Kururugi-V0.1-Back.jpg" width="40%" />
</p>

## ⚙️ Project overview

Kururugi is a high-performance ESC designed for FPV drones, built as a platform for low-level motor control, power electronics optimization, and embedded system experimentation.

It is developed within the **Bare Metal Foundry** initiative for autonomous racing drones, alongside the Shirley Flight Controller—forming a complete, fully controlled hardware stack.

This README present the project and its git repository, more technical details are available here: 📊 [Technical Specifications](docs/kururugi-specs-hw.md)

## ❓ Why Kururugi?

Most ESCs are closed, optimized for mass production—not understanding.

Kururugi is built to:
- Push switching frequency limits (96 kHz+ and further)
- Enable full control of the motor stack (no black boxes)
- Training on Bare Metal, super fast control (128kHz is aimed at long term), on low level hardware
- Having fun with crazy makers and scientists, you are more than welcome to join the adventure !

👉 This is an engineering platform, not just an ESC.

## 🚢 Navigate the repo

> ⚠️ Documentation is still a work in progress. Detailed design notes and analysis will be added progressively.

### 📂 Not to miss documents:
- 🧩 **Hardware (KiCad)**: 
- 📊 **Design & Sizing**: 
- 📤 **Exports**: [Schematics, PCB renders, STEP files](docs/exports)
- 📚 **Resources**: [Datasheets & Engineering Notes](resources/datasheets)

## 💻 Run it yourself

### 📟 H/W - PCB desgin
To get started, clone the repository and proceed to open the project on Kicad 9.0.4. All libraries are self containts so it is no more difficult than this.
```bash
git clone https://github.com/StepaneBinon/Kururugi-ESC-Dev-Board.git
```

### 📟 H/W — PCB Manufacturing

PCB fabrication and assembly are handled through JLCPCB. The minimum order quantity is 5 boards for PCB fabrication and 2 boards for assembly. With the selected options, the total cost is 255.13 € (295.01 USD).

Production files are available in [Production files](hardware/Kururugi-ESC-KiCad/Production/).  
When placing the order, use the parameters shown in [JLCPCB Options](hardware/Kururugi-ESC-KiCad/Production/JLCPCB%20-%20Options%20to%20use.png).

⚠️ Ensure BOM/CPL match and components are available before ordering.

### 🔢 S/W - Kururugi-6Step [WORK IN PROGRESS]


### 🔢 S/W - Kururugi-FOC [WORK IN PROGRESS]


## 🚀 Roadmap

### ⚡ Hardware
1. 🧪 **V1.0 Production & Bring-up** *(Apr 2026)*: First batch with JLCPCB, full electrical validation
2. 📖 **Design Documentation** *(May 2026)*: Complete breakdown of sizing, losses, and trade-offs
3. 🔌 **Multi-ESC Test Bench** *(Jun–Jul 2026)*: Dedicated rig for synchronized testing and stress scenarios
4. 🏁 **Drone Validation (V1)** *(Jul–Aug 2026)*: High-load FPV testing on 6-inch platform
5. 🤖 **Robotics Validation (V1)** *(Jul–Aug 2026)*: Differential drive platform for control robustness
6. 📝 **Technical Paper (V1 Results)** *(Sep 2026)*: Performance, efficiency, and design insights
7. ⚡ **V2 Hardware** *(2027)*: Sensor simplification, FOC/6-step optimization, possible 4-in-1

### 🧠 Software
1. 🏗 **Bare-Metal C++ Architecture** *(Apr–May 2026)*: Deterministic design, coding standards, HAL strategy (rewrite vs minimal use)
2. ⚙️ **6-Step Control (Baseline)** *(May–Jun 2026)*: Reliable startup and fallback mode
3. 🎯 **FOC Implementation** *(Jun–Aug 2026)*: High-efficiency, high-performance control
4. 📝 **Technical Paper (Control & Results)** *(Sep 2026)*: Control strategy, tuning, and comparison

## 🤝 How to Contribute

> Alone we go faster, together we go further.

Contributions are more than welcome, at all levels:

- 🐛 **Report issues**: bugs, design flaws, or unclear documentation
- 💡 **Suggest improvements**: hardware, firmware, or architecture ideas
- 🧪 **Test & validate**: share results, measurements, or flight data
- 📖 **Improve documentation**: clarify concepts, add explanations

### 📬 Get involved

- Open an issue for discussion  
- Submit a pull request  
- Share your insights or experiments 
- Join the **Bare Metal Foundry** discord here: https://discord.gg/e7CF2AZCN 

👉 If you're working on ESCs, power electronics, or embedded control, your feedback is highly valuable.

## 🪪 Licensing

### Hardware
Hardware design (schematics, PCB, KiCad) is licensed under CERN-OHL-S v2. This project is released as open hardware.
Any modifications or derived designs must be shared under the same license.

For commercial use, collaboration, or licensing inquiries, please reach out.



