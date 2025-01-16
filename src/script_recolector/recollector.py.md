import serial
import sqlite3
import curses
from datetime import datetime

# ESP32 connection on COM3
esp32_port = 'COM3'
baud_rate = 115200
try:
    ser = serial.Serial(esp32_port, baud_rate, timeout=1)
except serial.SerialException as e:
    print(f"Error connecting to port {esp32_port}: {e}")
    ser = None

# SQLite connection
conn = sqlite3.connect('device_detection.db')
cursor = conn.cursor()

# Create table if it doesn't exist
cursor.execute('''
    CREATE TABLE IF NOT EXISTS device_log (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        node INTEGER,
        mac_address TEXT,
        rssi INTEGER,
        timestamp TEXT,
        action TEXT
    )
''')
conn.commit()

# Dictionary to store detected devices
devices_in_range = {}

# Function to log devices into the database
def log_device(node, mac_address, rssi, action):
    timestamp = datetime.now().strftime('%Y-%m-%d %H:%M:%S')
    cursor.execute('''
        INSERT INTO device_log (node, mac_address, rssi, timestamp, action)
        VALUES (?, ?, ?, ?, ?)
    ''', (node, mac_address, rssi, timestamp, action))
    conn.commit()

# Function to process data from ESP32
def process_data(data):
    try:
        lines = data.strip().split('\n')
        detected_devices = []
        
        for line in lines:
            if "Node:" in line and "MAC:" in line and "RSSI:" in line:
                parts = line.split()
                node = int(parts[1])        # Assumes the node is at position 1
                mac_address = parts[3]      # Assumes the MAC address is at position 3
                rssi = int(parts[5])        # Assumes the RSSI is at position 5
                detected_devices.append((node, mac_address, rssi))

                # If the device is new, log as ENTRY
                if mac_address not in devices_in_range:
                    devices_in_range[mac_address] = rssi
                    log_device(node, mac_address, rssi, "ENTRY")
                else:
                    devices_in_range[mac_address] = rssi  # Update RSSI

        # Check devices no longer present
        current_devices = set(devices_in_range.keys())
        detected_now = set([device[1] for device in detected_devices])

        # Remove and log EXIT for devices out of range
        for mac_address in current_devices - detected_now:
            log_device(node, mac_address, devices_in_range[mac_address], "EXIT")
            del devices_in_range[mac_address]

        return detected_devices
        
    except Exception as e:
        print("Error processing data:", e)
        return []

# Function to display a decorative header
def display_header(stdscr):
    title = [
        " __________.____    ___________          _____________________________",
        " \\______   \\    \\   \\_   _____/         /   _____/\\_   _____/\\_   ___ \\",
        "  |     |  _/    |    |    __)_  ______  \\_____  \\  |    __)_/    \\  \\/",
        "  |     |   \\___ |    |        \\/_____/  /     \\ |        \\\\     \\____",
        "  |______  /_______\\_____/_______  /      /_______  //_______  / \\______  /",
        "         \\/          \\/          \\/               \\/         \\/         \\/"
    ]
    start_x = (curses.COLS - len(title[0])) // 2
    for i, line in enumerate(title):
        stdscr.addstr(i + 1, start_x, line, curses.A_BOLD | curses.color_pair(3))
    stdscr.addstr(8, start_x, "=" * len(title[0]), curses.A_BOLD)
    stdscr.addstr(9, (curses.COLS - len("COMTE DE RIUS")) // 2, "COMTE DE RIUS", curses.A_BOLD)
    stdscr.addstr(10, start_x, "=" * len(title[0]), curses.A_BOLD)
    stdscr.refresh()

# Function to display data on screen
def display_data(stdscr, detected_devices):
    title_1 = "BLE MAC RANGE"
    title_2 = "ENTRIES AND EXITS"
    
    # Center section titles
    title_1_x = 5 + (30 - len(title_1)) // 2
    title_2_x = 50 + (50 - len(title_2)) // 2
    
    # Section headers
    stdscr.addstr(12, title_1_x, title_1, curses.A_BOLD | curses.color_pair(1))
    stdscr.addstr(13, 5, "Node    MAC Address          RSSI", curses.A_BOLD | curses.color_pair(2))
    stdscr.addstr(14, 5, "-" * 40, curses.A_BOLD)

    stdscr.addstr(12, title_2_x, title_2, curses.A_BOLD | curses.color_pair(1))
    stdscr.addstr(13, 50, "Node    MAC Address          RSSI    Timestamp          Action", curses.A_BOLD | curses.color_pair(2))
    stdscr.addstr(14, 50, "-" * 65, curses.A_BOLD)

    # Display devices in the first column, limited to 10
    limited_devices = detected_devices[:10] if len(detected_devices) > 10 else detected_devices
    for idx, (node, mac_address, rssi) in enumerate(limited_devices):
        stdscr.addstr(15 + idx, 7, f"{node:<7}{mac_address:<19}")
        stdscr.addstr(15 + idx, 33, f"{rssi:^6}", curses.color_pair(6))

    # Display database logs in the second column
    cursor.execute("SELECT node, mac_address, rssi, timestamp, action FROM device_log ORDER BY timestamp DESC LIMIT 10")
    db_entries = cursor.fetchall()
    
    for idx, entry in enumerate(db_entries):
        node, mac_address, rssi, timestamp, action = entry
        color_pair = curses.color_pair(4) if action == "ENTRY" else curses.color_pair(5)
        stdscr.addstr(15 + idx, 52, f"{node:<7}{mac_address:<17}")
        stdscr.addstr(15 + idx, 78, f"{rssi:^6}", curses.color_pair(6))
        stdscr.addstr(15 + idx, 85, f"{timestamp:<20}")
        stdscr.addstr(15 + idx, 107, f"{action:<7}", color_pair)

    stdscr.refresh()

# Main loop with curses
def main(stdscr):
    curses.curs_set(0)
    stdscr.nodelay(True)
    stdscr.timeout(1000)

    curses.start_color()
    curses.init_pair(1, curses.COLOR_WHITE, curses.COLOR_BLACK)
    curses.init_pair(2, curses.COLOR_YELLOW, curses.COLOR_BLACK)
    curses.init_pair(3, curses.COLOR_CYAN, curses.COLOR_BLACK)
    curses.init_pair(4, curses.COLOR_GREEN, curses.COLOR_BLACK)
    curses.init_pair(5, curses.COLOR_RED, curses.COLOR_BLACK)
    curses.init_pair(6, curses.COLOR_YELLOW, curses.COLOR_BLACK)
    
    display_header(stdscr)
    
    while True:
        if ser and ser.in_waiting > 0:
            data = ser.read(ser.in_waiting).decode('utf-8')
            detected_devices = process_data(data)
        else:
            detected_devices = []

        display_data(stdscr, [(1, mac, devices_in_range[mac]) for mac in devices_in_range.keys()])
        
        if stdscr.getch() == ord('q'):
            break

try:
    curses.wrapper(main)
except KeyboardInterrupt:
    print("Program terminated by user.")
finally:
    if ser:
        ser.close()
    conn.close()
