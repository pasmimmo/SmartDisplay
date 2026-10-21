# 🔬 Hardware & Pinout Reference

Technical overview of the **SP-ESP32-S3-1.28-BOX** hardware and GPIO mappings.

---

## 📌 Complete GPIO Pinout Table

> [!WARNING]
> **Board Revisions Notice:** AliExpress vendors and clones occasionally update PCB revisions or route pins slightly differently (e.g., audio amplifier enable on GPIO46 vs GPIO48, or touch interrupt). Always verify your board's silkscreen markings if a peripheral does not initialize.

| GPIO Pin | Function | Subsystem | Description |
| :--- | :--- | :--- | :--- |
| **GPIO1** | ADC1 Channel 0 | Battery Monitor | 2:1 resistor divider. Voltage: 3.3V (0%) - 4.2V (100%). |
| **GPIO2** | SPI MOSI | Display (GC9A01A) | Data transmission to round LCD. |
| **GPIO4** | SPI CLK | Display (GC9A01A) | SPI clock boosted to 80MHz. |
| **GPIO5** | SPI CS | Display (GC9A01A) | Chip Select (Active LOW). |
| **GPIO6** | Touch Reset | Touchscreen (CST816D) | Hardware reset line. |
| **GPIO7** | I2C SCL (Touch) | Touchscreen (CST816D) | Dedicated Touch I2C bus clock. |
| **GPIO8** | I2S DOUT | Audio Speaker | Serial audio data to internal amplifier / DAC. |
| **GPIO9** | I2S BCLK | Audio Codec (ES8311) | Bit clock for I2S audio stream. |
| **GPIO11** | I2C SDA (Touch) | Touchscreen (CST816D) | Dedicated Touch I2C bus data. |
| **GPIO12** | Touch INT | Touchscreen (CST816D) | Interrupt signal triggered by touch events. |
| **GPIO14** | I2C SCL (Audio) | Audio Codec (ES8311) | Codec control bus clock. |
| **GPIO15** | I2C SDA (Audio) | Audio Codec (ES8311) | Codec control bus data (Address: `0x18`). |
| **GPIO16** | I2S MCLK | Audio Codec (ES8311) | Master clock output to the ES8311. |
| **GPIO38** | LCD Reset | Display (GC9A01A) | Display hardware reset line. |
| **GPIO41** | Digital Input | Charging Sensor | Logic HIGH when connected to USB-C 5V power. |
| **GPIO42** | LEDC PWM | Display Backlight | Active LOW (inverted: true). Frequency: 1000Hz. |
| **GPIO45** | I2S LRCLK | Audio Codec (ES8311) | Left/Right word select clock. |
| **GPIO46** | Digital Output | Audio Amplifier Power | Enables onboard speaker amplifier (Active HIGH). |
| **GPIO47** | LCD DC | Display (GC9A01A) | Data / Command control pin. |

---

## ⚡ Dynamic Audio Power Gating (GPIO46)
In standby mode, audio amplifiers consume ~20mA and produce an audible high-frequency hiss. 
SmartDisplay uses dynamic power management:
* Power amplifier (`GPIO46`) is set to `ALWAYS_OFF` in standby.
* When Home Assistant initiates a TTS voice stream, `GPIO46` turns ON.
* 1000ms after the audio stream finishes, `GPIO46` cuts power again.
