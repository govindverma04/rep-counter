# 🏋️‍♂️ Workout Rep Counter (ESP32 Based)

A standalone **hardware device** that automatically counts workout repetitions in real time using an ESP32 microcontroller.  
Designed for **push-ups, squats, sit-ups**, and similar exercises without relying on mobile apps or internet connectivity.

This project focuses on **reliability, real-time performance, and practical embedded-system design**.

---

## 📌 Features

- Automatic **rep counting** during workout sessions  
- **ESP32 hardware-timer based multiplexing** (stable, flicker-free display)  
- **2-digit 7-segment display** for live rep count  
- Distance-based motion detection with **noise filtering & hysteresis**
- Prevents **false and repeated counts**
- Adjustable **active zone & exit zone** for different exercises
- **Serial Monitor debugging** for calibration
- Optional **buzzer feedback** on each rep
- Fully **standalone hardware device**

---

## 🧠 How It Works

1. A Ultrasonic sensor continuously monitors body movement  
2. When the distance enters a predefined **active zone**, a rep is detected  
3. A **lock mechanism** ensures only one count per movement  
4. The system unlocks only after returning to a **free space zone**
5. The rep count updates instantly on the display  

This logic closely matches real human motion and avoids accidental double counts.

---

## 🛠 Hardware Components

- ESP32 Development Board  
- Ultrasonic sensor (HC SR04)  
- 2× Common-Anode 7-Segment Displays  
- Current-limiting resistors (220Ω / 330Ω)  
- Power stabilization capacitor (470uf recommended)  
- Optional piezo buzzer for audio feedback  

---

## 🔌 Pin Connections

| Component | ESP32 Pin |
|--------|-----------|
| Segment A–G | GPIO 13,12,14,27,26,25,33 |
| Digit Select | GPIO 32, 15 |
| Ultrasonic TRIG | GPIO 5 |
| Ultrasonic ECHO | GPIO 18 |
| Buzzer (optional) | GPIO 4 |

> Pin mapping can be changed easily in code.

---

## 💻 Software Details

- Written using **Arduino framework for ESP32**
- Uses **hardware timers** instead of `delay()` for display refresh
- Non-blocking architecture
- Sensor filtering to ignore invalid readings
- State-machine logic for accurate rep counting

---

## ▶️ Getting Started

### 1. Clone the repository
```bash
git clone https://github.com/govindveerma04e/rep-counter.git
```
### 2. Open in Arduino IDE

- Select the correct ESP32 board

- Set baud rate to 115200

### 3. Upload the code

- Connect ESP32 via USB

- Upload the sketch

### 4. Open Serial Monitor

- Use it to tune distance thresholds and stability parameters
---

## ⚙️ Configuration

- Key parameters you can adjust in code:

// Active movement range
ACTIVE ZONE: 30 – 80 cm

// Exit (free space) zone
EXIT ZONE: >110 cm

// Stability control
STABLE_REQUIRED = 2 or 3


- Adjust these values based on your exercise and sensor placement.
---

## 🏃 Supported Exercises

- Push-ups

- Squats

- Sit-ups

- Home workout routines

- DIY fitness tracking experiments
---
If you find this project useful, consider giving the repository a star ⭐
