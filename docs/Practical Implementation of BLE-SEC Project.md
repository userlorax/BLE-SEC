## Workflow of BLE-SEC
![[LORAWAN FINAL.png]]
![[PRACTICA FINAL.png]]
### 1. LoRa Nodes

The LoRa nodes (N1, N2, N3, N4) are the initial data capture points. Their main function is to continuously scan the environment for nearby devices, capturing two key data points:

1. **MAC Address**: Uniquely identifies each device.
2. **RSSI**: Measures the signal strength received from the device.

This information allows the system not only to identify which devices are present but also to estimate their proximity to the node. This is crucial for applications like security, where knowing who is in a specific area and their proximity to certain facilities is critical.


---

### 2. Data Collector Script

![]

The data collector script acts as an intermediary, processing raw data captured by the LoRa nodes into meaningful information. Key functions include:

- **Determining Entry or Exit**: Utilizes RSSI as the primary indicator. A continuous increase in RSSI suggests that the device is approaching (entry), while a continuous decrease suggests the opposite (exit).
- **Temporal Analysis**: Helps filter out momentary signal fluctuations that could result in false positives.

---

### 3. Databases (DB1 and DB2)

- **DB1 (Registry Database)**: Stores all relevant information about registered devices, including:
    
    - MAC Address
    - Personal data (e.g., Name, ID number)
    - Additional notes for future reference.
    
    Example Use Case: In a corporate environment, DB1 might contain employee details and their associated devices, enabling quick and accurate identification.
    
- **DB2 (Entry/Exit Database)**: Acts as a real-time log of all device activities detected, including:
    
    - Signal strength
    - The detecting node
    - Timestamp for chronological tracking.
    
    Example Use Case: In an access control system, DB2 would log every entry or exit, allowing precise auditing of activity.
    

---

### 4. Query and Logging Script

This script ensures the efficient operation and maintenance of the system by:

- **Registering New Devices**: Adds devices to DB1.
- **Continuous Monitoring**: Verifies DB2 to ensure only authorized devices are present.
- **Alerts**: Flags unauthorized devices or unusual device movements.

---

## LoRa Node Code Explanation

The code for the LoRa nodes performs periodic BLE scans of the nearby environment. For each detected device, it records and stores:

- **MAC Address**
- **RSSI**
- **Node Identifier** (to differentiate devices detected by different nodes).

The key steps in the code are:

1. **Initialization (`setup`)**:
    
    - Serial communication begins at 115200 baud for monitoring.
    - BLE scanning is initialized and set to active mode for detailed responses.
2. **Scanning (`loop`)**:
    
    - Scans for BLE devices for 5 seconds.
    - Counts and logs detected devices.
3. **Data Processing**:
    
    - Stores MAC Address, RSSI, and Node ID for each detected device.
    - Displays the data on the serial monitor for real-time monitoring.
4. **Cleanup**:
    
    - Clears scan results and pauses briefly before the next scan.

---

## Data Collector Script Explanation

The data collector script connects to an ESP32 on `COM3` and processes the BLE data into an SQLite database. It also provides real-time data visualization using `curses`.

### Key Components:

1. **ESP32 Connection**:
    
    - Establishes communication over a serial port with the ESP32.
    - Handles exceptions for connection issues.
2. **SQLite Integration**:
    
    - Creates a `device_log` table if it doesn’t exist.
    - Logs detected devices with attributes like Node ID, MAC Address, RSSI, Timestamp, and Action (Entry/Exit).
3. **Real-Time Processing**:
    
    - Detects new devices and logs them as entries.
    - Identifies devices that have left the range and logs them as exits.
4. **Data Visualization**:
    
    - Uses `curses` to display BLE device data and logs in two sections:
        - **Detected Devices**: Shows live RSSI and MAC Address data.
        - **Database Logs**: Displays the last 10 entries/exit events.

---

## Steps for Deployment

1. **Node Deployment**:
    
    - Install LoRa nodes in strategic locations, ensuring maximum coverage and overlap for seamless detection.
    - Configure Node IDs to differentiate data sources.
2. **Script Execution**:
    
    - Run the Python data collector script.
    - Ensure database connections and serial ports are correctly configured.
3. **System Monitoring**:
    
    - Monitor BLE data in real time via the terminal interface.
    - Regularly audit the databases for anomalies or unauthorized devices.

---

## Practical Applications

- **Urban Security**: Monitor public spaces to detect unusual patterns of movement.
- **Access Control**: Track authorized personnel in secured zones.
- **Event Management**: Analyze crowd dynamics in real-time.

---

## Conclusion

The practical implementation of BLE-SEC integrates hardware (LoRa nodes and ESP32), software (Python scripts and SQLite), and real-time monitoring tools to create a robust system for Bluetooth-based tracking and analysis. This practical approach ensures scalability, reliability, and adaptability to various use cases.