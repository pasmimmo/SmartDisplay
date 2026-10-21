# ❓ Troubleshooting & FAQ

---

## 1. GCC 14 Compiler Warnings
If building ESPHome with GCC 14 / ESP-IDF 5.x, you may encounter warnings:
* `-Wformat-truncation`: Fixed by sizing format buffers to `char buf[32]`.
* `%u` vs `uint32_t`: Fixed by explicit casting `(unsigned int)`.
* `StringRef` `.rfind()`: In ESPHome 2024.10+, API string arguments are `esphome::StringRef`. Use explicit `std::string(text_color.c_str())`.

---

## 2. Audio Hum or Crackle
* SmartDisplay uses dynamic power gating on `GPIO46`.
* Standby audio hum is completely eliminated because the amplifier is powered off when not in use.
* If you hear popping on audio start, verify that `delay: 100ms` exists before feeding I2S audio.

---

## 3. Screen Not Turning On
* Ensure USB-C cable supplies 5V and at least 500mA.
* Verify the backlight pin: `GPIO42` requires `inverted: true`.
* If double-tapping does not wake the screen, ensure CST816D touch interrupt on `GPIO12` is properly seated.

---

## 4. Weather Shows `--°C`
* Verify that your Home Assistant weather entity matches `weather_entity` under `substitutions:` in `smartdisplay.yaml`.
* If Home Assistant is offline, `--°C` is displayed safely without crashing.
