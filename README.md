# STM32L475E Real-Time Multi-Tasking Sensor Fusion & Telemetry System

![FreeRTOS](https://img.shields.io/badge/RTOS-FreeRTOS%20v10.x-blue.svg)
![Microcontroller](https://img.shields.io/badge/MCU-STM32L475VGT6-blue)
![Architecture](https://img.shields.io/badge/Architecture-ARM%20Cortex--M4F-red)
![Communication](https://img.shields.io/badge/Telemetry-MQTT%20%2F%20HiveMQ-orange)
![Developer](https://img.shields.io/badge/Developer-Nikunj%20Malik-brightgreen)
![License](https://img.shields.io/badge/License-MIT-green.svg)

Embedded real-time multi-tasking firmware designed for the **STM32L475E-IoT01A1** (B-L475E-IOT01A) development board. The system integrates sensor acquisition, real-time data fusion, low-power optimization, watchdog recovery, and wireless MQTT telemetry over Wi-Fi using FreeRTOS preemptive scheduling.

---

## 🏗️ System Architecture

### Software Stack
```
┌────────────────────────────────────────────────────────┐
│               MQTT Telemetry / HiveMQ                  │
├────────────────────────────────────────────────────────┤
│                 FreeRTOS Kernel Tasks                  │
│  [SensorAcq]   [DataFusion]   [Control]   [Telemetry]  │
├────────────────────────────────────────────────────────┤
│        Hardware Abstraction Layer (HAL / STM32L4)      │
├────────────────────────────────────────────────────────┤
│   LSM6DSL (IMU)   |   LPS22HB (Press)   |  HTS221 (Env) │
└────────────────────────────────────────────────────────┘
```

### Preemptive Multi-Task Schedule

| Task Name | Priority | Period | Description |
| :--- | :--- | :--- | :--- |
| **`SensorAcq`** | Highest (4) | 10 ms | High-frequency 6-axis IMU & environmental data sampling |
| **`Fusion`** | High (3) | 20 ms | Sensor fusion filter and orientation state estimation |
| **`Control`** | High (3) | 20 ms | Real-time threshold monitoring and control logic |
| **`Telemetry`** | Medium (2) | 100 ms | Serial and Wi-Fi MQTT JSON payload transmission |
| **`Watchdog`** | Low (1) | 1000 ms | System health check and task heartbeat monitor |

---

## ⚡ Hardware Platform

- **Evaluation Board**: STMicroelectronics B-L475E-IOT01A
- **Core Microcontroller**: STM32L475VGT6
  - ARM 32-bit Cortex-M4F with FPU operating at up to 80 MHz
  - 1 MB Flash memory, 128 KB SRAM
- **Integrated Sensors**:
  - **LSM6DSL**: 3D accelerometer (±2g to ±16g) and 3D gyroscope (±125dps to ±2000dps)
  - **LPS22HB**: Barometric absolute pressure sensor (260 to 1260 hPa)
  - **HTS221**: Relative humidity (0 to 100%) and ambient temperature sensor
- **Connectivity**:
  - Inventek ISM43362-M3G-L44 Wi-Fi module (802.11 b/g/n)
  - USART / Virtual COM port for high-speed diagnostic telemetry

---

## 📊 Performance Benchmarks

- **Deterministic Latency**: Task execution latency guaranteed `< 50 ms` under full sensor payload.
- **Power Optimization**:
  - Implements **FreeRTOS Tickless Idle** and low-power sleep modes.
  - Active Mode: ~15 mA @ 3.3V
  - Idle Mode: ~8 mA @ 3.3V
  - Low-Power Sleep: ~2 mA @ 3.3V
  - Net power reduction: ~12% overall power savings compared to standard polling.
- **Telemetry Throughput**: Real-time MQTT stream publishing at 10 Hz with formatted JSON payloads.

---

## 🧰 Diagnostic & Testing Suite

The repository includes a comprehensive Python and PowerShell test suite in the `tools/` directory:

1. **`tools/mqtt_monitor.py`**:
   - Subscribes to telemetry topics on the MQTT broker (HiveMQ).
   - Real-time terminal dashboard and live Matplotlib data plotting.
   - Automated CSV logging for offline statistical analysis.
2. **`tools/performance_test.py`**:
   - Automated benchmark harness over serial interface.
   - Evaluates task latency, jitter, CPU utilization, and memory leak detection.
3. **`tools/measure_power.ps1`**:
   - Power consumption estimation and duty-cycle modeling across operating modes.
4. **`build.py`**:
   - Cross-platform build automation script for environment verification, compilation, and flashing.

---

## 📁 Repository Structure

```
stm32-rtos-sensor-fusion/
├── firmware/
│   ├── src/
│   │   ├── main.c                  # FreeRTOS kernel initialization & scheduler
│   │   ├── main.h                  # System configuration and common types
│   │   ├── FreeRTOSConfig.h        # Tuned FreeRTOS kernel configuration
│   │   └── tasks/
│   │       ├── sensor_acq.c/.h     # Real-time sensor acquisition task
│   │       └── ...
│   └── drivers/
│       ├── lsm6dsl.c/.h            # Thread-safe LSM6DSL IMU driver (I2C)
│       └── ...
├── tools/
│   ├── mqtt_monitor.py             # Live telemetry visualization tool
│   ├── performance_test.py         # Latency & throughput benchmark suite
│   └── measure_power.ps1           # Power consumption profiler
├── build.py                        # Automated build & flash CLI
├── requirements.txt                # Python dependencies for diagnostic tools
├── .gitignore                      # Git ignore file for build & Python artifacts
└── README.md                       # Project documentation
```

---

## 🚀 Quick Start

### 1. Requirements
- Python 3.8+
- ARM GNU Toolchain (`arm-none-eabi-gcc`) or STM32CubeIDE
- ST-Link CLI or STM32CubeProgrammer

### 2. Install Tool Dependencies
```bash
pip install -r requirements.txt
```

### 3. Build & Verify Environment
```bash
python build.py check
```

### 4. Run Telemetry Monitor
```bash
# Connect to HiveMQ MQTT broker and stream sensor telemetry
python tools/mqtt_monitor.py --plot --csv telemetry_log.csv
```

---

## 👨‍💻 Author

**Nikunj Malik**
- GitHub: [@nikunjmalik306](https://github.com/nikunjmalik306)
- Email: [nikunjmalikkrm@gmail.com](mailto:nikunjmalikkrm@gmail.com)

---

## 📜 License

This project is licensed under the [MIT License](LICENSE).
