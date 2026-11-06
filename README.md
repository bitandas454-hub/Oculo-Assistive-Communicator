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
