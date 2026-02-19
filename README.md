# Air-measurement-project

### 1. The Core: ESP32 Microcontroller

The ESP32 was chosen as the "brain" of the project due to its dual-core processing power and built-in connectivity. <br>

Multitasking: It simultaneously handles three heavy tasks: reading serial data from sensors, maintaining a high-frequency refresh rate for the LED matrix, and maintaining a WiFi connection to upload data to the cloud. <br>

Communication: It uses multiple Hardware UART (Universal Asynchronous Receiver-Transmitter) ports to talk to the sensors. This allows for dedicated, interference-free communication channels for both CO2 and Particulate Matter (PM) data. <br> 

### 2. CO2 Sensing: MH-Z19B

The MH-Z19B is a specialized sensor used to measure Carbon Dioxide (CO2) levels in parts per million (PPM). It works on the Non-Dispersive Infrared principle. It has an infrared source and a detector; since CO2 molecules absorb specific wavelengths of IR light, the sensor calculates the concentration based on the amount of light that reaches the detector. <br>

Integration: The ESP32 requests data via UART at 9600 baud. The code then parses the return signal to extract the PPM value.<br>

### 3. Particulate Matter Sensing: NextPM (Terra Sensor)

The NextPM sensor is a high-end industrial component used to detect PM1.0, PM2.5, and PM10. <br>

Laser Scattering: Inside the sensor, a laser beam hits airborne particles. The light scatters in different directions depending on the size of the particles. An optical detector measures this scattered light, and a microprocessor converts it into mass concentration (μg/m3). <br>

Hexadecimal Protocol: Unlike simpler sensors, the NextPM communicates using raw hexadecimal frames. The ESP32 sends a specific 3-byte command (0x81, 0x12, 0x6D) and must carefully parse a 16-byte response, verifying a checksum to ensure the data wasn't corrupted during transmission.<br>

### 4. Data Visualization: PxMatrix 64x32

To make the data accessible, we use a 64x32 RGB LED Matrix driven by the PxMatrix library.<br> 

Scanning and Refresh: Because the matrix cannot light up all LEDs at once (to save power and prevent overheating), it uses a scanning method. The ESP32 uses an internal timer interrupt to refresh the screen every few milliseconds, tricking the human eye into seeing a steady image (persistence of vision).<br>

Color Mapping (RGB565): The system uses a 16-bit color depth. As discussed previously, we implemented a "swap" function to adapt the standard RGB565 format to the screen's specific RBG wiring, ensuring that the colors (Green for healthy, Red for alert) are displayed correctly.<br>

### 5. Connectivity and Cloud Integration

Beyond local display, the project is a true IoT (Internet of Things) device.<br>

WiFi Stack: The ESP32 connects to the internet via WPA2 Enterprise (Eduroam) or standard WiFi.<br>

InfluxDB Cloud: Every 10 seconds, the measurements are packaged into a "Line Protocol" format and sent to an InfluxDB database. This allows for long-term data logging and the creation of remote dashboards to analyze air quality trends over weeks or months.


