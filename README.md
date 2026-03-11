# 🔥 Smart Wireless Forest Fire Alerting System

> A **Wireless Sensor Network** based system for early detection and real-time alerting of forest fires — developed as a BSc thesis at General Sir John Kotelawala Defence University, Sri Lanka (2021)

![C](https://img.shields.io/badge/C-00599C?style=flat-square&logo=c&logoColor=white)
![Arduino](https://img.shields.io/badge/Arduino-00979D?style=flat-square&logo=arduino&logoColor=white)
![IoT](https://img.shields.io/badge/IoT-WSN-blue?style=flat-square)
![RF](https://img.shields.io/badge/RF-433MHz%20%7C%202.4GHz-orange?style=flat-square)
![GSM](https://img.shields.io/badge/Alerts-GSM%20SMS-green?style=flat-square)

---

## 📋 Overview

Forest fires cause irreversible damage to biodiversity and ecosystems. This project addresses the lack of an effective, low-cost early detection system — particularly relevant for Sri Lanka, where 50–200 forest fires are reported annually.

The system uses a **distributed Wireless Sensor Network (WSN)** of smart sensor nodes deployed inside a forest. The nodes monitor temperature and humidity, compute the **Heat Index**, and alert relevant authorities via **SMS** when dangerous thresholds are exceeded — fully automatically, with no human intervention required.

> 🏆 All 7 project objectives were completed successfully. The prototype covers over **600 m²** of forest and is fully scalable.

---

## 🏗️ System Architecture

The system consists of 3 components working together:

```
 ┌─────────────┐    HC-12 (433MHz)    ┌─────────────┐   NRF24L01+ (2.4GHz)   ┌──────────────┐
 │ Sensor Node │ ──────────────────►  │  Sink Node  │ ──────────────────────► │ Base Station │
 │  (in forest)│ ◄──────────────────  │ (in forest) │                         │(outside,GSM) │
 └─────────────┘   inter-node check   └─────────────┘                         └──────┬───────┘
                                                                                      │ SIM800L
                                                                                      ▼
                                                                             📱 SMS Alert to
                                                                             Wildlife Dept /
                                                                             Forest Dept /
                                                                             Fire Brigade
```

### Component Roles

| Component | Location | Responsibility |
|-----------|----------|----------------|
| **Sensor Node** | Inside forest | Read temp & humidity → compute Heat Index → alert Sink Node |
| **Sink Node** | Inside forest | Relay alerts from Sensor Nodes → forward to Base Station |
| **Base Station** | Outside forest (GSM coverage area) | Receive alerts → send SMS to authorities |

---

## ⚡ Novel Feature: Smart Sensor (Inter-Node Communication)

A key innovation of this system is the **Smart Sensor concept** — sensor nodes don't just monitor the environment, they also monitor *each other*.

Each sensor node periodically sends a status signal to its neighbor node. If no response is received, the node alerts the Sink Node of the neighbor's failure. This gives the system **self-awareness** — authorities are notified not only of fires but also of hardware failures in the field.

```
Sensor Node 1  ──── check status ────►  Sensor Node 2
               ◄─── active signal ─────

If no response → Send NODE FAILURE ALERT to Sink Node
```

Each alert type is distinct — the Sink Node and Base Station handle **Heat Alerts** and **Node Failure Alerts** separately.

---

## 🛠️ Hardware Components

| Component | Module | Role |
|-----------|--------|------|
| Microcontroller (Sensor Node) | Arduino Nano (ATmega328, 16MHz, 32KB Flash) | Reads sensors, computes heat index, manages inter-node comms |
| Microcontroller (Sink + Base) | Arduino Mega | Manages dual transceivers and GSM module |
| Temperature & Humidity Sensor | **DHT-11** | Reads temperature (0–50°C) and humidity (30–90% RH) |
| Inter-node / node-to-sink RF | **HC-12** (433.4–473 MHz, 100 channels) | Half-duplex, up to 1km open range, UART/TTL, AT-configurable |
| Sink-to-base station RF | **nRF24L01+** (2.4 GHz ISM) | PA+LNA, up to 1.2km range, 125 channels, 2Mbps max |
| SMS Alert Module | **SIM800L** Quad Band GSM/GPRS | Sends SMS with Heat Alert / Node Alert / Node Details |
| Power (design) | Solar panels | One per node for fully off-grid autonomous operation |

---

## 🌡️ Heat Index Calculation

The DHT-11 cannot reliably detect extreme fire temperatures directly. Instead, the system computes the **Heat Index** — a combined function of temperature and humidity — to detect dangerous ambient conditions before a fire spreads:

```
HI = c1 + c2T + c3R + c4TR + c5T² + c6R² + c7T²R + c8TR² + c9T²R²
```

Where `T` = temperature (°C) and `R` = relative humidity (%).

**Threshold: 45°C Heat Index** — chosen because the maximum survivable human body temperature is ~42.2°C, making this a reliable danger marker.

Library used: `Adafruit DHT.h`

---

## 📡 Communication Design

Two radio frequencies are used deliberately to isolate network segments and optimize range:

| Link | Module | Frequency | Tested Effective Range |
|------|--------|-----------|----------------------|
| Sensor ↔ Sensor | HC-12 | 433 MHz | ✅ Up to **50m** (100% success) |
| Sensor → Sink Node | HC-12 | 433 MHz | ✅ Up to **50m** (100% success) |
| Sink Node → Base Station | nRF24L01+ | 2.4 GHz | ✅ Up to **1.2 km** (80%+) |

Each sensor node uses **separate HC-12 channels** for inter-node communication vs. sink-node alerts — preventing data collision and interference.

Libraries used: `SoftwareSerial.h`, `SPI.h`, `nRF24L01.h`, `RF24.h`

---

## 📊 Test Results

### Range Testing

| Components | Distance | Success Rate |
|------------|----------|-------------|
| Sensor ↔ Sensor (HC-12) | 1m – 50m | ✅ 100% |
| Sensor ↔ Sensor (HC-12) | 100m | ⚠️ 80% |
| Sensor ↔ Sensor (HC-12) | 500m | ⚠️ 60% |
| Sensor ↔ Sensor (HC-12) | 1 km | ❌ 0% |
| Sink ↔ Base Station (NRF24L01+) | 1m – 1 km | ✅ 100% |
| Sink ↔ Base Station (NRF24L01+) | 1.2 km | ⚠️ 80% |
| Sink ↔ Base Station (NRF24L01+) | 1.5 km | ❌ 0% |

**→ Optimal sensor node spacing: 50m — placed at vertices of a polygon, sink node at center**

### Accuracy Testing (DHT-11 Detection Distance per Fire Size)

| Fire Type | Detection Distance |
|-----------|-------------------|
| Candle flame | 1–2 m |
| Small fire (burning paper) | 3–5 m |
| Medium fire (household trash) | 6–10 m |
| Large fire (dry wood campfire) | 10–15 m |

### System Response Time

> ⚡ Average time from threshold breach to **SMS received by authorities: ~4.5 seconds**

---

## 🚀 Getting Started

### Prerequisites
- Arduino IDE
- Libraries: `Adafruit DHT`, `SoftwareSerial`, `SPI`, `nRF24L01`, `RF24`
- Hardware: Arduino Nano ×2, Arduino Mega ×2, DHT-11 ×2, HC-12 ×3, nRF24L01+ ×2, SIM800L ×1

### Upload to Sensor Node

```cpp
// Key config in sensor_node.ino
#define DHTPIN 2
#define DHTTYPE DHT11
#define HC12_TX 10
#define HC12_RX 11
#define HEAT_INDEX_THRESHOLD 45.0  // degrees Celsius

// Channel config (AT commands via SET pin)
// Channel A: inter-node communication
// Channel B: alert to Sink Node
```

```bash
# 1. Open sensor_node/sensor_node.ino in Arduino IDE
# 2. Select Board: Arduino Nano | Port: your COM port
# 3. Upload
```

### Upload to Sink Node

```bash
# 1. Open sink_node/sink_node.ino
# 2. Select Board: Arduino Mega
# 3. Upload
```

### Upload to Base Station

```bash
# 1. Open base_station/base_station.ino
# 2. Set your target phone number:
#    String authorityNumber = "+94XXXXXXXXX";
# 3. Select Board: Arduino Mega
# 4. Upload
```

---

## 📁 Repository Structure

```
smart-forest-fire-wsn/
├── sensor_node/
│   └── sensor_node.ino         # DHT-11 reading, Heat Index calc, HC-12 comms
├── sink_node/
│   └── sink_node.ino           # HC-12 receive, NRF24L01+ transmit
├── base_station/
│   └── base_station.ino        # NRF24L01+ receive, SIM800L SMS dispatch
├── docs/
│   └── thesis.pdf              # Full BSc thesis (2021)
├── circuit_diagrams/
│   ├── sensor_node_circuit.png
│   ├── sink_node_circuit.png
│   └── base_station_circuit.png
└── README.md
```

---

## 📋 Project Objectives Status

| Objective | Status |
|-----------|--------|
| Critical review of existing systems & technologies | ✅ Completed |
| Design & develop a Smart Wireless Forest Fire Alerting System prototype | ✅ Completed |
| Detect and alert a forest fire as early as possible | ✅ Completed (~4.5s response) |
| Send alert to relevant authorities immediately via SMS | ✅ Completed |
| Establish inter-node communication between sensor nodes | ✅ Completed (Smart Sensor) |
| Fully automated and accurate system | ✅ Completed |
| Evaluate the system phase by phase | ✅ Completed |

---

## ⚠️ Known Limitations

- Prototype covers ~600 m² (2 sensor nodes); more clusters needed for large forests
- Base station must be placed where GSM coverage is available (outside dense forest cover)
- HC-12 effective range drops below 100% beyond 50m — LoRa upgrade recommended for production
- Animal activity or physical damage to nodes requires manual maintenance

---

## 🔮 Future Enhancements

- 📍 Add **GPS module** per node for precise fire geo-location in SMS alerts
- 🤖 Integrate **Machine Learning** to predict fire risk from sensor trends
- 🚁 Beacon signal for **aerial observer coordination**
- 🌐 Web dashboard for real-time multi-node monitoring
- 📡 **LoRa** module upgrade for greater inter-node range in production deployment

---

## 📄 Academic Context

| | |
|-|-|
| **Thesis Title** | Smart Wireless Forest Fire Alerting System |
| **Degree** | BSc (Hons) Computer Engineering |
| **University** | General Sir John Kotelawala Defence University, Sri Lanka |
| **Year** | 2021 |
| **Supervisor** | Dr. T.L. Weerawardane |
| **Key Technologies** | WSN, HC-12, nRF24L01+, DHT-11, SIM800L, Arduino |

---

<div align="center">Made with ❤️ by <a href="https://github.com/nipunrodrigo">Nipun Rodrigo</a> | Tampere, Finland 🇫🇮</div>
