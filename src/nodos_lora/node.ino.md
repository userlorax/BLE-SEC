#include <Arduino.h>
#include <BLEDevice.h>

#define MAX_DEVICES 100  // Maximum number of devices that can be stored
#define NODE_ID 1        // Change this number to differentiate each node

// Structure to store device information
struct BLEDeviceInfo {
  String macAddress;
  int rssi;
  int node;  // Added the node to identify the device
};

// Array to store detected devices
BLEDeviceInfo detectedDevices[MAX_DEVICES];
int deviceCount = 0;

void setup() {
  Serial.begin(115200);
  Serial.println("Starting BLE scan...");

  BLEDevice::init("");
  BLEScan* pBLEScan = BLEDevice::getScan();
  pBLEScan->setActiveScan(true);
}

void loop() {
  BLEScan* pBLEScan = BLEDevice::getScan();
  BLEScanResults* foundDevices = pBLEScan->start(5, false);
  Serial.printf("Devices found: %d\n", foundDevices->getCount());

  deviceCount = 0;  // Reset the device count for each scan

  // Process the devices found in this scan
  for (int i = 0; i < foundDevices->getCount() && deviceCount < MAX_DEVICES; i++) {
    BLEAdvertisedDevice device = foundDevices->getDevice(i);
    detectedDevices[deviceCount].macAddress = device.getAddress().toString().c_str();
    detectedDevices[deviceCount].rssi = device.getRSSI();
    detectedDevices[deviceCount].node = NODE_ID;  // Assign the node
    deviceCount++;

    // Display data on the serial monitor
    Serial.print("Node: ");
    Serial.print(detectedDevices[deviceCount - 1].node);
    Serial.print(" MAC: ");
    Serial.print(detectedDevices[deviceCount - 1].macAddress);
    Serial.print(" RSSI: ");
    Serial.println(detectedDevices[deviceCount - 1].rssi);
  }

  // Clear results and pause before the next scan
  pBLEScan->clearResults();
  delay(100);  // Wait 1 second before scanning again
}
