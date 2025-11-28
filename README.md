🚦 Smart Traffic Control System for Emergency Vehicles
AI + IoT Vehicle Counting using Custom YOLOv8 Model & ESP32

This project implements a smart traffic control system that detects the number of cars in real-time using an ESP32-CAM video feed and a custom-modified YOLOv8 model.
The detection results are sent to an ESP32 controller, which decides whether the traffic light turns green or red based on congestion level.

Designed to prioritize ambulances and emergency vehicles, the system automatically opens the road when traffic reaches a defined threshold.

✨ Features

✔ Real-time car detection using a custom YOLOv8 model

✔ Video streaming from ESP32-CAM

✔ Car count sent over WiFi to ESP32

✔ Automatic traffic light control (RED/GREEN)

✔ Live count shown on 0.96" OLED display (SSD1306)

✔ Custom AI model developed and modified by Eng. Youssef Yasser

🧠 System Architecture
ESP32-CAM → Python YOLOv8 Detection → ESP32 Controller → LEDs + OLED


ESP32-CAM streams video

Python script performs YOLOv8 car detection

Car count sent via HTTP to an ESP32

ESP32 controls traffic LEDs

OLED displays "Car Count" in real time

🛠 Hardware Requirements

ESP32 Dev Module

ESP32-CAM

OLED Display (SSD1306, I2C)

Red LED

Green LED

220Ω resistors

Jumper wires

🔌 Wiring Diagram
ESP32 → OLED (SSD1306 I2C)
OLED Pin	ESP32 Pin
VCC	3.3V
GND	GND
SDA	GPIO 21
SCL	GPIO 22
ESP32 → LEDs
LED	ESP32 Pin	Resistor
Red LED (+)	GPIO 25	220Ω
Green LED (+)	GPIO 26	220Ω
LED GND	GND	—
🖥 Python YOLOv8 Detection (PC Side)
Installation
pip install ultralytics opencv-python requests numpy

Run the detection script
python detect.py


This script:

Reads the stream: http://ESP32-CAM-IP/stream

Runs YOLOv8 on each frame

Counts number of cars

Sends count to ESP32:

http://ESP32-IP/update?count=12

📟 ESP32 Traffic Controller Code

The ESP32 receives the car count and:

Condition	Action
carCount >= 10	GREEN ON, RED OFF
carCount < 10	RED ON, GREEN OFF

Also updates OLED display with live numbers.

Upload the provided esp32_traffic_controller.ino file.

🚀 How It Works

ESP32-CAM streams video

Python script reads the stream

YOLOv8 detects vehicles

Car count sent to ESP32

ESP32 switches LEDs

OLED displays the count

System automatically opens road for emergency vehicles

📊 Results

Stable car counting at 20–30 FPS

Accurate YOLOv8 detection

Fast LED switching (<50ms)

Suitable for smart-city or real emergency use

🔮 Future Improvements

Add ambulance-specific detection

Add MQTT cloud dashboard

Add GSM module for remote alerts

Train a dedicated model for cars in Egypt

🧑‍💻 Developer
**👨‍🔧 Engineered & AI-Developed by:
👉 Eng. Youssef Yasser**

Designed system architecture

Modified and integrated YOLOv8 custom model

Developed ESP32 firmware

Built full AI + IoT pipeline

Optimized for emergency-vehicle priority systems

⭐ If you like this project

Please star ⭐ the repository on GitHub — it helps a lot!
