# 🔥 Smart Wireless Forest Fire Alerting System

> Real-time **Wireless Sensor Network** for early forest fire detection using microcontrollers, FreeRTOS, and live telemetry

![C++](https://img.shields.io/badge/C++-00599C?style=flat-square&logo=cplusplus&logoColor=white)
![FreeRTOS](https://img.shields.io/badge/FreeRTOS-8CC84B?style=flat-square)
![IoT](https://img.shields.io/badge/IoT-Enabled-blue?style=flat-square)
![MQTT](https://img.shields.io/badge/MQTT-Protocol-orange?style=flat-square)

---

## 📋 Overview

This system uses a mesh of low-power wireless sensor nodes deployed across forested areas to detect environmental conditions indicating fire risk. Data is transmitted in real-time to a central monitoring station with instant alerting.

### Key Capabilities
- 🌡️ Temperature, humidity, and smoke detection per node
- 📡 Multi-hop wireless mesh network (low power)
- ⚡ Real-time alert system with threshold-based triggers
- 🖥️ Central monitoring dashboard
- 🔋 Energy-efficient design using FreeRTOS task scheduling

---

## 🏗️ System Architecture

```
[Sensor Node 1] ─┐
[Sensor Node 2] ─┼──► [Gateway Node] ──► [Cloud/Dashboard]
[Sensor Node 3] ─┘         │
                            └──► [Local Alert System]
```

---

## 🛠️ Hardware Components

| Component | Purpose |
|-----------|---------|
| Microcontroller (ESP32 / STM32) | Processing & communication |
| DHT22 Sensor | Temperature & humidity |
| MQ-2 Gas Sensor | Smoke / combustible gas |
| LoRa Module | Long-range wireless communication |
| Solar Panel + LiPo | Power supply |

---

## ⚙️ Software Stack

- **RTOS:** FreeRTOS (task-based concurrency)
- **Language:** C / C++
- **Protocol:** MQTT over LoRa/WiFi
- **Backend:** Node.js + MQTT broker (Mosquitto)
- **Dashboard:** React.js with real-time WebSocket updates

---

## 📦 FreeRTOS Task Design

```c
// Main tasks running concurrently
void sensorReadTask(void *pvParameters);   // Read sensor data every 5s
void communicationTask(void *pvParameters); // Transmit via MQTT
void alertTask(void *pvParameters);        // Check thresholds & alert
void powerMgmtTask(void *pvParameters);    // Deep sleep management
```

---

## 🚀 Getting Started

### Prerequisites
- PlatformIO or Arduino IDE
- ESP32 / STM32 board
- FreeRTOS (included in ESP-IDF)

### Build & Flash

```bash
# Using PlatformIO
pio run --target upload

# Monitor serial output
pio device monitor --baud 115200
```

---

## 📊 Results

- ✅ Detection latency: **< 3 seconds** from smoke exposure
- ✅ Network range: **up to 2km** per node hop
- ✅ Battery life: **72+ hours** on a single charge
- ✅ False positive rate: **< 2%** with threshold tuning

---

## 📄 Research Paper

This project was part of academic research on WSN architectures for environmental monitoring.
See `https://www.researchgate.net/publication/393601103_Smart_Wireless_Forest_Fire_Alerting_System` for full methodology and results.

---

<div align="center">Made with ❤️ by <a href="https://github.com/nipunrodrigo">Nipun Rodrigo</a> | Tampere, Finland 🇫🇮</div>
