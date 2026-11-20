Smart Parking Availability System
An ESP32-based smart parking prototype that tracks vehicle entry and
exit, controls an automatic gate, displays parking information on an
OLED screen, and provides a live dashboard through a local web server.
Features
- Vehicle counting: Two IR sensors detect vehicles entering and
  leaving the parking area.
- Capacity management: The prototype supports a maximum capacity
  of 2 cars.
- Automatic gate: An SG90-compatible servo opens the gate when a
  car enters or exits. The gate closes automatically after 3 seconds.
- OLED display: Shows occupied spaces, available spaces, parking
  status, and gate status.
- Buzzer alert: Sounds when another vehicle is detected at the
  entrance while the parking area is full.
- Wi-Fi dashboard: Hosts a responsive web page on the ESP32's
  local IP address.
- Live updates: The dashboard requests updated system status every
  second.
Hardware Required
- ESP32 development board
- 2 IR obstacle/vehicle detection sensor modules
- 0.96-inch I2C OLED display (SSD1306, 128 × 64)
- Servo motor (for example, SG90)
- Active buzzer
- Jumper wires
- USB cable and a USB power source
Power note: The example is designed around an ESP32 powered
through USB. A servo can draw more current than the board's 3.3 V pin
can safely provide. If the ESP32 resets, the servo jitters, or the
display behaves unpredictably, use a suitable separate regulated 5 V
supply for the servo and connect the supply ground to ESP32 GND. Do
not connect a 5 V signal to an ESP32 GPIO.

Wiring
  Component         Component pin                                            ESP32 pin
  Entry IR sensor   OUT                                                        GPIO 33
Exit IR sensor    OUT                                                        GPIO 32
Both IR sensors   VCC                        According to the sensor module's rating
Both IR sensors   GND                                                            GND
OLED              SDA                                                        GPIO 21
OLED              SCK / SCL                                                  GPIO 22
OLED              VDD                          According to the OLED module's rating
OLED              GND                                                            GND
Servo             Signal (orange/yellow)                                     GPIO 18
Servo             VCC (red)                                      Suitable 5 V supply
Servo             GND (brown/black)                                              GND
Buzzer            +                                                         GPIO 23
Buzzer            −                                                              GND

All components must share a common ground. Check the voltage
requirements of your specific IR modules and OLED before connecting
their VCC pins.
Arduino IDE Setup
1. Install the Arduino IDE.
2. Install the ESP32 board package and select the appropriate ESP32
   board (for example, ESP32 Dev Module).
3. Install these libraries from Library Manager:
   - Adafruit GFX Library
   - Adafruit SSD1306
   - ESP32Servo
4. WiFi.h, WebServer.h, and Wire.h are included with the ESP32
   Arduino core.
5. Connect the ESP32 over USB and select the correct board and COM
   port.
6. Open the project sketch, update the Wi-Fi credentials, and upload
   it.
Wi-Fi Configuration
Update these lines near the top of the sketch:
const char* ssid = "YOUR_WIFI_NAME";
const char* password = "YOUR_WIFI_PASSWORD";
The ESP32 connects to the Wi-Fi network in station mode. Your computer
or phone must be connected to the same network to open the dashboard.

Security: Do not publish your real Wi-Fi password in a public
repository. Replace it with placeholders before sharing the code.
Running the Project
1. Power on the ESP32.
2. Open Arduino IDE Serial Monitor and set the baud rate to 115200.
3. Wait for the Wi-Fi connection message.
4. Find the IP ADDRESS printed in the Serial Monitor.
5. Open a browser on a device connected to the same Wi-Fi network.
6. Visit http://<ESP32-IP-ADDRESS>.
The dashboard displays the occupied-car count, available-space count,
two slot indicators, entry/exit sensor states, gate status, and overall
parking status. It refreshes automatically every second.
How It Works
Vehicle entry
- The entry IR sensor (GPIO 33) detects a vehicle.
- The code applies a 300 ms debounce interval to reduce repeated
  triggers.
- If capacity is available, the occupied count increases and the gate
  opens.
- If the parking area is already full, the gate remains closed and the
  buzzer alert is activated when a vehicle is detected at the
  entrance.
Vehicle exit
- The exit IR sensor (GPIO 32) detects a vehicle.
- If the occupied count is greater than zero, the count decreases and
  the gate opens.
- The gate closes automatically after 3 seconds.
Display and web dashboard
- The OLED shows occupied and available counts, parking status, and
  gate state.
- The ESP32 serves the dashboard at /.
- The /status endpoint returns the current parking data as JSON.
- The browser polls /status once per second to refresh the
  dashboard.
Important Configuration in the Sketch
  Setting                              Current value Purpose
  MAX_CAPACITY                                 2 Maximum vehicle count

  IR_IN                                       33 Entry sensor GPIO

  IR_OUT                                      32 Exit sensor GPIO

  SERVO_PIN                                   18 Servo signal GPIO

  BUZZER_PIN                                  23 Buzzer GPIO

  OLED_ADDR                                 0x3C OLED I2C address

  IR_ACTIVE_LOW                             true Treats LOW as sensor
detection

  DEBOUNCE_TIME                           300 ms Minimum interval
between sensor
triggers
GATE_OPEN_TIME                         3000 ms Gate open duration
If your IR module outputs HIGH when an object is detected, change
IR_ACTIVE_LOW to false.
Current Prototype Limitation
The current sketch maintains a total vehicle count, not independent
detection for each physical parking bay. The dashboard labels Slot 1
occupied when the count is at least 1 and Slot 2 occupied when the count
is at least 2. These are count-based indicators, not separate per-slot
sensor readings.

The count-based approach assumes each vehicle is detected once at the
entry and once at the exit. Sensor placement, alignment, and testing are
important to avoid missed or duplicate detections.
Troubleshooting
- ESP32 does not connect to Wi-Fi: Check the SSID and password.
  Ensure the network is compatible with the ESP32's 2.4 GHz Wi-Fi.
- Dashboard does not open: Confirm that the phone/computer and
  ESP32 are on the same network, and use the IP address printed in
  Serial Monitor.
- IR sensor behaves in reverse: Change IR_ACTIVE_LOW to false,
  if required by your module.
- IR sensor triggers repeatedly: Adjust sensor positioning and
  sensitivity, and review the debounce interval.
- OLED is blank: Check SDA/SCL wiring, power, ground, and the I2C
  address (0x3C).
- Servo jitters or ESP32 restarts: Use a suitable external 5 V
  servo supply with a common ground.
- Buzzer does not sound: Check polarity and confirm that the
  buzzer type is compatible with direct GPIO control. Use a suitable
  driver if its current requirement exceeds GPIO limits.
Future Improvements
- Use a separate sensor for each parking slot to report true
  individual bay occupancy.
- Add cloud connectivity or a mobile application for remote
  monitoring.
- Add vehicle entry/exit event history.
- Improve gate safety with vehicle-presence checks and obstruction
  handling.
- Add configurable parking capacity and alert settings.
Project Summary
This project demonstrates a small-scale smart parking system using an
ESP32, IR sensors, an OLED display, a servo-operated gate, a buzzer, and
a local Wi-Fi dashboard. It provides real-time local monitoring and
basic automatic gate control for a two-car prototype.