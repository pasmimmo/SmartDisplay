# 🔮 SmartDisplay ESP32-S3 v1.0.0

Initial production release of **SmartDisplay**, an open-source, modular firmware based on **ESPHome** and **LVGL 8** designed for the **SP-ESP32-S3-1.28-BOX** circular desktop display.

This firmware repurposes the commercial AliExpress AI desktop bot (originally running cloud-locked firmware from `78/xiaozhi-esp32`) into a **100% local, private, full-featured Home Assistant notification display, standby clock, weather widget, and TTS voice announcement speaker**.

---

## ✨ What's New & Key Highlights

### 🎨 Display & UI (LVGL 8)
* **Tailored Circular Geometry:** Designed specifically for the 240x240 GC9A01A round IPS display.
* **80MHz SPI Bus:** Fast framebuffer transfer eliminating LVGL render delays (`[W][component:419]`).
* **Centered Circular Diagnostics:** Redesigned system info screen with symmetric metrics and cyan header.
* **Auto-Home on Sleep:** Automatically navigates back to the Standby Clock (`home_page`) whenever the display turns off or sleeps.

### 📬 Smart Notification System
* **LIFO Notification Queue:** Stores up to 10 notifications in memory with interactive card carousel.
* **Material Symbols Outlined:** Integrated vector glyphs (`home`, `warning`, `info`, `thermometer`, `window`, `lightbulb`, `lock`, `notifications`, etc.).
* **Dynamic Color Engine:** Supports color names (`white`, `cyan`, `green`, `orange`, `red`, etc.) and hex codes.
* **Visual Confirmation Dialog:** Dedicated modal to dismiss all cards (Green NO to keep, Red SI to clear).
* **Unread Notification Badge:** Subtle amber bell above the standby clock when notifications are pending.

### 🔊 Audio & Voice Announcements
* **Everest ES8311 DAC & I2S:** Seamless streaming via Home Assistant's `announcement_pipeline`.
* **Dynamic Power Gating (GPIO46):** Automatically cuts amplifier power in standby, saving ~20mA and eliminating background hiss.

### ⛅ Standby Clock & Live Weather
* **Event-Driven SNTP Clock:** Synchronized over Wi-Fi, updates strictly on minute rollover.
* **Live Weather Widget:** Subscribes to Home Assistant `weather.*` entity with automatic condition-to-icon mapping and thematic accent colors.

### 📡 Passive Bluetooth Proxy
* **Long-Range BLE Extension:** Forwards nearby Bluetooth sensors (e.g. **Xiaomi Mi Flora plant care sensors**, BTHome thermometers, BLE beacons) directly to Home Assistant.
* **Passive Mode (`active: false`):** Captures 100% of sensor telemetry with zero connection overhead and no audio stuttering.

### 🔋 Power & Battery Management
* **Battery ADC Filter:** Median window filtering on GPIO1 for stable voltage reporting.
* **USB-C Charging Detection:** GPIO41 sensing; stays permanently awake on desk when plugged in, enters auto-sleep when on battery.
* **1kHz Flicker-Free PWM Backlight:** Dimmable via Home Assistant `light.smartdisplay_backlight`.

---

## 🏠 Home Assistant Integration

### 1-Click Blueprint Import
[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fpasmimmo%2FSmartDisplay%2Fblob%2Fmain%2Fblueprint%2Fsmartdisplay_notification.yaml)

```
https://github.com/pasmimmo/SmartDisplay/blob/main/blueprint/smartdisplay_notification.yaml
```

### Action Call Example
```yaml
action: esphome.smartdisplay_send_notification
data:
  message: "Front door was unlocked!"
  icon: "lock_open"
  text_color: "orange"
  timeout: 30
```

---

## 🛒 Hardware & Compatibility
* **Verified Board:** SP-ESP32-S3-1.28-BOX (ESP32-S3, 16MB Flash, 8MB Octal PSRAM, GC9A01A LCD, CST816D Touch, ES8311 DAC).
* See [Hardware Pinout & Technical Reference](docs/HARDWARE.md) for full 19-GPIO schematic details.

---

## 🤝 Contributors
* **@pasmimmo** (Creator & Maintainer)
* **Antigravity (Google DeepMind)** (AI Collaborator)
* **78/xiaozhi-esp32** (Stock Hardware Insights)
