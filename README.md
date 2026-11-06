# OculoComm: Low-Cost Assistive Eye-Blink Communication System

OculoComm is an open-source, dual-ESP32 prototype designed to help non-verbal or paralyzed patients (such as those with ALS or severe stroke recovery needs) communicate basic daily needs to caregivers. 

By detecting intentional long eye blinks using a wearable ESP32-CAM module, the system sends wireless alert signals over ESP-NOW to a bedside hub. The bedside hub displays the requested need on an OLED screen and triggers audio beeps for the caregiver.

---

## 1. The Problem & Why This Project Matters

### The Problem
Patients who lose their voice and motor capabilities often struggle to signal basic human needs like needing water, being in pain, or calling for help. 
* **High Cost of Existing Tools:** Commercial eye-tracking devices (like dedicated gaze-trackers) are very expensive, costing hundreds to thousands of dollars.
* **Privacy Issues:** Many modern app-based or cloud-connected systems require streaming camera video or personal health logs to online servers.
* **Hardware Overload:** Trying to run heavy camera processing, wireless streaming, and screen display logic on a single small board causes system lag and crashes.

### The Solution
OculoComm splits the work across two cheap microcontrollers:
1. **Node 1 (Wearable):** Handles camera frames right on the board to spot intentional blinks.
2. **Node 2 (Bedside Hub):** Receives signals wirelessly and displays patient requests safely by the bedside.

---

## 2. System Architecture & How It Works

The project uses a simple two-node setup connected via **ESP-NOW**—a direct, fast wireless communication protocol provided by Espressif that does not need a Wi-Fi router or internet access.
+------------------------------------+           ESP-NOW           +----------------------------------+
|   Node 1: Wearable Optic Unit      |  ========================>  |    Node 2: Bedside Companion Hub |
|   (ESP32-CAM on Glasses/Headband)  |   Direct Wireless Signals   |    (Standard ESP32 Dev Module)   |
|   - Captures close-up eye frame    |                             |    - Receives incoming alert data|
|   - Calculates brightness (ROI)    |                             |    - Cycles OLED dynamic menu    |
|   - Filters normal vs. long blink  |                             |    - Sounds protective buzzer tone|
+------------------------------------+                             +----------------------------------+


### Technical Workflow
1. **Camera Input:** The ESP32-CAM focuses on the user's eye area (Region of Interest / ROI).
2. **Blink Detection:** When the eye closes, the total brightness of that area drops noticeably. 
3. **Blink Filtering:** Natural, subconscious blinks are fast (typically under 0.3 seconds). The code only registers an intentional trigger if the eye stays closed for longer than **1.2 seconds**.
4. **Wireless Transmission:** Once a valid long blink is detected, Node 1 sends a small telemetry packet to Node 2's physical MAC address.
5. **Bedside Action:** Node 2 receives the packet, highlights the selected item on the OLED menu (`[ Water ]`, `[ Pain ]`, `[ Nurse Help ]`), and plays a buzzer tone.

---

## 3. Component List & Cost Breakdown

This prototype was built using low-cost hardware components totaling around **₹1,400 to ₹1,500 ($18 USD)**.

| Component | Function | Estimated Cost |
| :--- | :--- | :--- |
| **ESP32-CAM (with OV2640 Camera)** | Captures eye frames & processes blinks | ~₹450 ($5.50) |
| **Standard ESP32 Dev Board** | Bedside receiver & display controller | ~₹400 ($5.00) |
| **0.96" I2C OLED Display (SSD1306)** | Displays menu items for patient/caregiver | ~₹200 ($2.50) |
| **Piezo Buzzer / Small Speaker** | Plays alert tones (wired with a 220Ω resistor) | ~₹50 ($0.60) |
| **Jumper Wires & Power Cable** | Circuit connections & 5V USB power supply | ~₹300 ($3.50) |
| **Total Build Cost** | | **~₹1,400 ($17.10)** |

---

## 4. Hardware Pinout & Wiring (Bedside Hub)

To keep the wiring clean on a breadboard, Node 2 is wired using pins on the **Right Side** of the standard ESP32 Dev Board:

* **3V3 / VIN:** Connected to OLED VCC pin.
* **GND:** Connected to OLED GND pin and Speaker (-) ground line.
* **GPIO 21 (SDA):** Connected to OLED SDA pin.
* **GPIO 22 (SCL):** Connected to OLED SCL pin.
* **GPIO 23:** Connected to Speaker (+) through a **220-ohm protective resistor** (limits current to protect the ESP32 pin).

---

## 5. Repository Structure

```text
OculoComm/
├── firmware/
│   ├── node1_wearable_cam/       # Arduino C++ code for ESP32-CAM (ROI thresholding & sender)
│   └── node2_bedside_hub/        # Arduino C++ code for Bedside ESP32 (OLED interface & buzzer receiver)
├── hardware/
│   ├── schematics/               # Wiring pinout diagrams and single-side setup notes
│   └── bill_of_materials.md      # Detailed component vendor list and cost sheet
├── docs/                         # Prototype design notes, diagrams, and competition slides
├── .gitignore                    # Build output and temporary file filter
├── LICENSE                       # MIT Open-Source License
└── README.md                     # Main repository documentation
```


## 6. How to Build & Flash the Code

### Prerequisites
1. Download and install **Arduino IDE** (v1.8.19 or v2.x).
2. Install the **ESP32 Board Package** (`Tools > Board Manager > Search ESP32 by Espressif`).
3. Install required libraries via Arduino Library Manager:
   * `Adafruit_GFX`
   * `Adafruit_SSD1306`

### Flashing Order
1. **Get Receiver MAC Address:** 
   Upload a basic Wi-Fi MAC retrieval sketch to your standard ESP32 Dev Board. Note down the physical MAC address printed on the Serial Monitor.
2. **Flash Node 2 (Bedside Hub):**
   Open `firmware/node2_bedside_hub/node2_bedside_hub.ino`, select `ESP32 Dev Module`, and upload.
3. **Flash Node 1 (Optic Unit):**
   Open `firmware/node1_wearable_cam/node1_wearable_cam.ino`, paste the Bedside Hub MAC address into the `broadcastAddress` array, select `AI Thinker ESP32-CAM`, and upload using an FTDI board adapter.

---

## 7. Current Prototype Limitations & Future Steps

Because this is a functional proof-of-concept prototype built under tight time constraints, it currently has a few practical limits:

* **Lighting Sensitivity:** The ROI brightness thresholding works best under steady room light. Changes in ambient shadows may require recalibrating threshold values.
* **Basic Speaker Output:** Uses a simple current-limited resistor connection for audio beeps rather than a full I2S DAC audio amplifier board.
* **Breadboard Prototyping:** Components are currently wired on breadboards rather than a custom Printed Circuit Board (PCB).

### Next Steps
* Add auto-calibration so the camera adapts dynamically to room lighting shifts.
* Design a custom compact PCB and 3D-printed headband mount.
* Add deep-sleep modes to save battery power on the wearable node.

---

## 8. License

This project is open-source under the **MIT License**. Feel free to use, modify, and build upon this hardware and software design.


