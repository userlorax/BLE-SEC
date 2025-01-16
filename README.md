# BLE-SEC

## Representative Phrase

"Intelligent infrastructure based on LoRaWAN and ESP32 for Bluetooth device detection and people flow analysis, enhancing urban safety."

---

## Introduction

Accelerated urbanization has increased pedestrian traffic and, consequently, security risks in cities. BLE-SEC addresses this challenge by detecting and anonymously tracking Bluetooth devices, enabling the identification of suspicious behavior patterns. This system, based on low-power and long-range technology, is designed to proactively improve urban safety.

---

## Key Technologies

1. **ESP32**: Bluetooth (BLE) device detection.
2. **LoRaWAN**: Long-distance data transmission with low power consumption.
3. **Data Analysis Algorithms**: Identifying suspicious patterns in the data.
4. **SQLite Databases**: Real-time event logging and querying.

---

## Project Structure

### Directories and Files

```plaintext
BLE-SEC/
├── README.md               # Project introduction and summary.
├── docs/                   # Theoretical and technical documentation.
│   ├── PracticoProyecto.md # Practical information.
│   ├── TeoricoProyecto.md  # Theoretical information.
├── src/                    # Project source code.
│   ├── nodos_lora/         # Code related to LoRa nodes.
│   │   ├── nodos_lora.ino  # LoRa nodes code.
│   ├── script_recolector/  # Scripts for data collection and processing.
│       ├── recolector.py   # Python data collector script.
├── db/                     # Project SQLite databases.
│   ├── device_detection.db # Device detection database.
├── diagrams/               # Technical diagrams and schemes.
│   ├── flujo_trabajo.png   # BLE-SEC workflow diagram.
├── LICENSE                 # Project license.
├── .gitignore              # Files and folders to ignore by Git.
```

---

## Getting Started

### Requirements

- **Hardware**:
    
    - ESP32.
    - LoRa modules.
    - Antennas and power sources.
- **Software**:
    
    - Arduino IDE (for programming ESP32).
    - Python 3.x (for running collector scripts).
    - SQLite (for managing databases).

### Installation

1. **Clone the repository**:
    
    ```bash
    git clone https://github.com/userlorax/BLE-SEC.git
    cd BLE-SEC
    ```
    
2. **Set up the development environment**:
    
    - Install the required libraries for Arduino and ESP32.
        
    - Install Python dependencies
        
        
3. **Upload code to LoRa nodes**:
    
    - Use the `src/nodos_lora/nodos_lora.ino` file to configure ESP32 devices.
4. **Run the data collector script**:
    
    - Execute `src/script_recolector/recolector.py` to collect and log data into the SQLite database.

---

## Contribution

1. Fork the repository.
    
2. Create a branch for your changes:
    
    ```bash
    git checkout -b feature/new_feature
    ```
    
3. Submit a Pull Request explaining your changes.
    

---

## License

This project is licensed under the Non-Commercial Use License.  
You are free to use, modify, and distribute this project **for non-commercial purposes**.  
For commercial use, please contact us.


---
