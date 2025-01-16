# BLE-SEC

## Overview

This project provides a **BLE device detection and logging system** designed to track and manage Bluetooth Low Energy (BLE) devices. It is particularly useful for applications like access control, crowd monitoring, and urban security. Our focus is on delivering a Python-based **data collector script** that logs BLE devices' entry and exit events into a database.

---

## How It Works

The system operates using two primary components:

### 1. **BLE Detection Script** (Runs on ESP32)

This script is deployed on an **ESP32** microcontroller to scan for nearby BLE devices. It captures:

- **MAC Address**: Unique identifier of the device.
    
- **RSSI (Received Signal Strength Indicator)**: Measures signal strength to estimate proximity.
    
- **Node ID**: Identifies the scanning node if multiple are used.
    

The detected data is sent via serial communication to the data collector for processing.

### 2. **Data Collector Script** (Python)

Our **data collector script** runs on a computer or server and processes the data from the ESP32. It manages the logic to determine whether a device is entering or exiting the monitored area and logs this information into an SQLite database.

#### Key Features:

- **Entry and Exit Logic**: Based on the RSSI value trends:
    
    - A new device detected is marked as "ENTRY."
        
    - A device no longer detected is marked as "EXIT."
        
- **Real-Time Processing**: Continuously updates the database with live device activity.
    
- **Database Logging**: Stores details such as MAC Address, RSSI, timestamp, and action.
    

---

## Provided Components

We provide the **Python-based data collector script**, which includes:

1. **Real-Time BLE Device Processing**:
    
    - Reads data from an ESP32 via a serial port.
        
    - Implements entry and exit logic.
        
2. **Database Integration**:
    
    - Uses SQLite to log detected devices and their activities.
        
3. **Visualization**:
    
    - Displays detected devices and recent database logs in real time using a terminal-based interface.
        

> **Note**: The BLE detection script for ESP32 is not included but can be easily implemented following examples available online. The focus of this project is on providing the data processing and storage layer.

---

## Usage Instructions

### Requirements

- **Hardware**:
    
    - ESP32 with BLE scanning capabilities.
        
    - USB connection to a computer.
        
- **Software**:
    
    - Python 3.x
        
    - Required Python libraries (see below).
        

### Installation

    
1. Install dependencies:
    
    ```
    pip install pyserial sqlite3 curses
    ```

---

## Example Database Schema

The SQLite database includes the following table:

`**device_log**`:

- `id`: Unique identifier.
    
- `nodo`: Node ID where the device was detected.
    
- `mac_address`: BLE device's MAC Address.
    
- `rssi`: Signal strength of the detected device.
    
- `timestamp`: Date and time of detection.
    
- `action`: `ENTRY` or `EXIT`.
    

---

## Use Cases

- **Access Control**: Track authorized devices entering or exiting a secure area.
    
- **Crowd Monitoring**: Analyze movement patterns of people carrying BLE-enabled devices.
    
- **Urban Security**: Detect unusual or suspicious movement in public spaces.
    

---

## License

This project is licensed under a Non-Commercial Use License. You may use, modify, and distribute the software for **non-commercial purposes only**.

---

