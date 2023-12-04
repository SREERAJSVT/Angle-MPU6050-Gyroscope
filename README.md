# Angle-MPU6050-Gyroscope
To measure the angle using an MPU6050 gyroscope and display it on an I2C OLED display
To measure the angle using an MPU6050 gyroscope and display it on an I2C OLED display, you'll need the following components:

**Components:**
1. Arduino board (e.g., Arduino Uno)
2. MPU6050 Gyroscope
3. I2C OLED Display (128x64 pixels)
4. Jumper wires

**Circuit Connections:**

Connect the components as follows:

- Connect the VCC and GND of the MPU6050 to the 5V and GND on the Arduino.
- Connect the SDA and SCL of the MPU6050 to the respective SDA and SCL pins on the Arduino.
- Connect the VCC and GND of the OLED display to the 5V and GND on the Arduino.
- Connect the SDA and SCL of the OLED display to the respective SDA and SCL pins on the Arduino.

**Arduino IDE Code:**

Make sure you have the necessary libraries installed. You can install them through the Library Manager in the Arduino IDE.

1. Install the "Wire" library for I2C communication.
2. Install the "Adafruit_GFX" and "Adafruit_SSD1306" libraries for the OLED display.

Here is a sample Arduino IDE code to measure the angle using the MPU6050 and display it on the OLED display:

```
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <MPU6050.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET    -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

MPU6050 mpu;

void setup() {
  Serial.begin(9600);
  Wire.begin();
  mpu.initialize();

  if (!display.begin(SSD1306_I2C_ADDRESS, OLED_RESET)) {
    Serial.println(F("SSD1306 allocation failed"));
    for (;;);
  }

  display.display();
  delay(2000);  // Pause for 2 seconds
  display.clearDisplay();
}

void loop() {
  float AccX, AccY, AccZ;
  float gyroX, gyroY, gyroZ;
  float accAngleX, accAngleY;
  float gyroAngleX, gyroAngleY, gyroAngleZ;
  float roll, pitch;

  mpu.getMotion6(&AccX, &AccY, &AccZ, &gyroX, &gyroY, &gyroZ);

  accAngleX = atan(AccY / sqrt(pow(AccX, 2) + pow(AccZ, 2))) * 180 / PI;
  accAngleY = atan(-1 * AccX / sqrt(pow(AccY, 2) + pow(AccZ, 2))) * 180 / PI;

  gyroAngleX = gyroX / 131;
  gyroAngleY = gyroY / 131;
  gyroAngleZ = gyroZ / 131;

  roll = 0.96 * (gyroAngleX + gyroX * 0.005) + 0.04 * accAngleX;
  pitch = 0.96 * (gyroAngleY + gyroY * 0.005) + 0.04 * accAngleY;

  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 0);
  display.print("Roll: ");
  display.print(roll);
  display.setCursor(0, 10);
  display.print("Pitch: ");
  display.print(pitch);
  display.display();

  Serial.print("Roll: ");
  Serial.print(roll);
  Serial.print("   ");
  Serial.print("Pitch: ");
  Serial.println(pitch);

  delay(500); // Adjust the delay as needed for your application
}
```

Make sure to adjust the I2C address in the OLED display initialization line (`display.begin(SSD1306_I2C_ADDRESS, OLED_RESET)`) if your OLED display has a different address.

Upload this code to your Arduino board and open the Serial Monitor to view the roll and pitch values. The OLED display will also show the real-time roll and pitch angles.
