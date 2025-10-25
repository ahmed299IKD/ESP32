# ESP32 Advanced Security Platform v4.0 - Red Team Edition

This project is a major upgrade to the existing ESP32 security platform, transforming the device into an advanced Wi-Fi pentesting tool, ideal for academic Red Team vs. Blue Team exercises.

The architecture has been expanded to include advanced attack capabilities and a vulnerability scanner, all controlled via a modern and responsive web interface.

## 1. New Features in v4.0 (Red Team)

This version introduces four main attack and analysis modules, enabling realistic simulation of wireless threats.

| Module | Description | Red Team Objective | Blue Team Objective |
| :--- | :--- | :--- | :--- |
| **Deauth Attack Advanced** | Sending targeted (unicast) or broadcast deauthentication frames to disconnect clients from an access point. | Test client and access point resilience to wireless denial-of-service (DoS) attacks. | Detect management frame floods and implement countermeasures (802.11w PMF). |
| **Frame Fuzzer** | Generate malformed 802.11 frames (Beacon, Probe Request, Auth, etc.) with a configurable mutation rate. | Discover buffer overflow vulnerabilities or unexpected behavior in router WiFi stack implementations. | Implement strict validation of received 802.11 frames and monitor unexpected router reboots. |
| **WPA3 Handshake Capture** | Enable promiscuous mode to capture WPA3-SAE key exchanges and EAPOL handshakes. | Recover data needed for offline brute force (dictionary) attacks against WPA/WPA2/WPA3 passwords. | Ensure passwords are long and complex, and that offline attack protection mechanisms (such as WPA3's *Dragonfly Handshake*) are enabled. |
| **Router Vulnerability Scanner** | Passively scan WiFi networks to identify common vulnerabilities (weak encryption, WPS enabled, hidden SSID, etc.) and assess the risk level. | Quickly identify the easiest targets to compromise in the Blue Team environment. | Use the scanner results to harden the configuration of their access points (Blue Team). |

## 2. Technical Architecture

The project uses the modern FreeRTOS/ESP-IDF architecture to ensure stability and performance.

* **Backend (C++/FreeRTOS):** The attack modules are implemented as separate FreeRTOS tasks, allowing for concurrent and non-blocking execution. Communication with the web interface is via **WebSocket** and **JSON** format for increased robustness.
* **Frontend (HTML/Alpine.js):** The user interface is entirely web-based (`index_advanced.html`, `app.js`, `style.css`), providing real-time control without the need for a mobile or desktop application.

## 3. Installation and Usage Guide

This project is configured for **PlatformIO**.

### 3.1. Prerequisites

* An ESP32 module (ESP32-S3 is recommended for better WiFi performance).
* VSCode with the PlatformIO extension.
* The ESP-IDF framework (automatically managed by PlatformIO).

### 3.2. Build and Download Steps

1. **Copy Files:** Ensure all project files (including the new `.h` and `.cpp` modules and the web interface in `data/`) are in your PlatformIO project directory.
2. **PlatformIO Configuration:** Check the `platformio.ini` file to ensure the target environment (e.g., `esp32s3box`) is correct.
3. **Firmware Build:**
```bash
pio run -e <your_environment>
```
4. **File System Download (Web UI):**
```bash
pio run -e <your_environment> -t uploadfs
```
5. **Firmware Download:**
```bash
pio run -e <your_environment> -t upload
```

### 3.3. Platform Usage

1. After flashing, the ESP32 will start an Access Point (AP).
* **Default SSID:** `ESP32_Security_Platform_v4`
* **Default Password:** `password123`
2. Connect your computer or phone to this WiFi network. 3. Open your browser and navigate to the address: `http://192.168.4.1`
4. The advanced web interface will load. Use the side menu to access the various attack modules (Deauth, Fuzzing, WPA3 Capture, Scanner).
5. **For the Red Team:** Use the attack modules to simulate compromise scenarios.
6. **For the Blue Team:** Use the **Router Vulnerability Scanner** to identify weaknesses in your own network defense (and fix them), then use the logs to detect attack attempts.

## 4. C++ Code Integration (Preview)

The new C++ modules have been added to the `include/` and `src/` directories.

### 4.1. `DeauthAttackAdvanced`

C
