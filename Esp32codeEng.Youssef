#include <WiFi.h>
#include <WebServer.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

// ========= WIFI SETTINGS =========
const char* ssid     = "ssid";      // <-- change this
const char* password = "pass";  // <-- change this

// ========= OLED SETTINGS =========
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET   -1
#define OLED_ADDR    0x3C   // most SSD1306 I2C modules

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

// ========= PINS =========
const int RED_PIN   = 25;   // red LED
const int GREEN_PIN = 26;   // green LED

// car threshold
const int CAR_THRESHOLD = 10;

int carCount = 0;

WebServer server(80);

// ---------- update LEDs + OLED ----------
void updateOutputs() {
  // LED logic
  if (carCount > CAR_THRESHOLD) {
    digitalWrite(GREEN_PIN, HIGH);
    digitalWrite(RED_PIN, LOW);
  } else {
    digitalWrite(GREEN_PIN, LOW);
    digitalWrite(RED_PIN, HIGH);
  }

  // OLED
  display.clearDisplay();
  display.setTextSize(2);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 0);
  display.println("Cars:");
  display.setCursor(0, 30);
  display.println(carCount);
  display.display();
}

// ---------- /update?cars=xxx ----------
void handleUpdate() {
  if (!server.hasArg("cars")) {
    server.send(400, "text/plain", "Missing 'cars' parameter");
    return;
  }

  carCount = server.arg("cars").toInt();
  updateOutputs();
  server.send(200, "text/plain", "OK");
}

// ---------- optional root handler ----------
void handleRoot() {
  String msg = "ESP32 car counter\n\n";
  msg += "Send: /update?cars=<number>\n";
  msg += "Current cars: " + String(carCount);
  server.send(200, "text/plain", msg);
}

void setup() {
  Serial.begin(115200);
  delay(500);

  // LED pins
  pinMode(RED_PIN, OUTPUT);
  pinMode(GREEN_PIN, OUTPUT);
  digitalWrite(RED_PIN, LOW);
  digitalWrite(GREEN_PIN, LOW);

  // I2C for OLED
  Wire.begin(21, 22);  // SDA = 21, SCL = 22

  if (!display.begin(SSD1306_SWITCHCAPVCC, OLED_ADDR)) {
    Serial.println("SSD1306 init failed");
    while (true) {
      // blink LEDs fast to show error
      digitalWrite(RED_PIN, HIGH);
      digitalWrite(GREEN_PIN, HIGH);
      delay(200);
      digitalWrite(RED_PIN, LOW);
      digitalWrite(GREEN_PIN, LOW);
      delay(200);
    }
  }

  // Boot screen
  display.clearDisplay();
  display.setTextSize(2);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 0);
  display.println("Booting...");
  display.display();

  // WiFi connect
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);

  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(0, 0);
  display.println("Connecting WiFi...");
  display.display();

  Serial.print("Connecting to WiFi");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWiFi connected!");

  // Show IP
  IPAddress ip = WiFi.localIP();
  Serial.print("ESP32 IP: ");
  Serial.println(ip);

  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(0, 0);
  display.println("WiFi OK");
  display.setCursor(0, 12);
  display.print("IP: ");
  display.println(ip.toString());
  display.setCursor(0, 30);
  display.print("Thresh: ");
  display.println(CAR_THRESHOLD);
  display.display();

  // Web server routes
  server.on("/", handleRoot);
  server.on("/update", handleUpdate);
  server.begin();

  // initial state
  carCount = 0;
  updateOutputs();
}

void loop() {
  server.handleClient();
}
