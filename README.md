# 🔮 MiniDisplay ESP32-S3

[![ESPHome Version](https://img.shields.io/badge/ESPHome-2024.10%2B-blue.svg)](https://esphome.io)
[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Compatible-41BDF5.svg)](https://home-assistant.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Hardware](https://img.shields.io/badge/Hardware-SP--ESP32--S3--1.28--BOX-orange.svg)](https://it.aliexpress.com/item/1005008821384965.html)

**MiniDisplay** is a modern, modular, open-source firmware based on **ESPHome** and **LVGL 8** designed for the **SP-ESP32-S3-1.28-BOX** circular smart display. 

It repurposes the popular AliExpress [Xiaozhi AI desktop box](https://it.aliexpress.com/item/1005008821384965.html) (originally running cloud-locked firmware from [78/xiaozhi-esp32](https://github.com/78/xiaozhi-esp32)) into a **100% local, private, full-featured Home Assistant notification display, desktop clock, weather widget, and TTS voice announcement speaker**.

---

## ✨ Features

* **🎨 Smooth Circular UI (LVGL 8)**: Tailored specifically for the 240x240 GC9A01A round IPS display.
* **📬 LIFO Notification Queue**: Stores up to 10 notifications in an interactive card carousel with Material Symbols icons and customizable text colors.
* **🔊 Voice Announcements (TTS & Media Player)**: Built-in ES8311 DAC + I2S speaker integrated with Home Assistant's `announcement_pipeline`.
* **⛅ Standby Clock + Live Weather**: Event-driven digital clock synchronized over SNTP + live outdoor condition icon and temperature fetched directly from Home Assistant.
* **🔔 Unread Notification Badge**: Subtle amber bell indicator above the clock when unread notifications are waiting in the queue.
* **⚡ Smart Power & Battery Management**:
  * Accurate battery level reporting with an ADC median filter (`GPIO1`).
  * Hardware charging detection (`GPIO41`).
  * Auto-sleep timer (default: 300s) turns off the screen **only** when running on battery; stays awake on desk when plugged in.
  * **Dynamic Audio Power**: Automatically cuts amplifier power (`GPIO46`) in standby, saving **~20mA** and eliminating background hiss.
* **🎛️ Dimmable PWM Backlight**: 1kHz flicker-free LEDC control with smooth fade-in/fade-out transitions and Home Assistant brightness slider support.
* **👆 Natural Touch Gestures**: Browse history, toggle screen, open diagnostics, or clear notifications with intuitive swipes.

---

## 🛒 Hardware & Purchase Link

| Item | Details |
| :--- | :--- |
| **Product Link** | [AliExpress ESP32-S3 1.28" Display Box](https://it.aliexpress.com/item/1005008821384965.html) |
| **Original Stock FW** | [78/xiaozhi-esp32 (GitHub)](https://github.com/78/xiaozhi-esp32) |
| **SoC** | Espressif ESP32-S3 (8MB Octal PSRAM, 16MB Flash) |
| **Display** | 1.28-inch IPS Circular LCD (240x240, GC9A01A over SPI) |
| **Touchscreen** | CST816D capacitive touch (I2C) |
| **Audio DAC** | Everest ES8311 + onboard speaker amp |
| **Battery** | Internal rechargeable 1S LiPo + USB-C charging port |

👉 For full pinout tables, schematics, and factory firmware backup instructions, see **[Hardware Reference](docs/HARDWARE.md)**.

---

## 🚀 Quickstart

Want to get running in under 3 minutes? Check our **[⚡ Lapidary Quickstart Guide](docs/QUICKSTART.md)**!

### Installation via ESPHome Dashboard

1. In your Home Assistant ESPHome Dashboard, click **+ New Device**, name it `minidisplay`, and skip the wizard.
2. Click **Edit** and paste the content of [`minidisplay_standalone.yaml`](minidisplay_standalone.yaml) (or use the modular [`packages/`](packages/) setup).
3. Set your parameters in `substitutions:` at the top:
   ```yaml
   substitutions:
     device_name: "minidisplay"
     friendly_name: "MiniDisplay"
     timezone: "Europe/Rome"
     weather_entity: "weather.forecast_casa"
     sleep_timeout: "300s"
   ```
4. Put your Wi-Fi credentials into `secrets.yaml`:
   ```yaml
   wifi_ssid: "YOUR_WIFI_SSID"
   wifi_password: "YOUR_WIFI_PASSWORD"
   ```
5. Click **Install** -> **Plug into this computer** (or update **Wirelessly** if already running ESPHome).

---

## 🏠 Home Assistant Integration

### 1. Sending a Notification
Call the `esphome.<device_name>_send_notification` action from any automation or script:

```yaml
action: esphome.minidisplay_send_notification
data:
  message: "Front door was unlocked!"
  icon: "lock_open"
  text_color: "orange"
  timeout: 30
```

#### Supported Notification Icons
| Icon Keyword | Material Symbol | Recommended Use |
| :--- | :---: | :--- |
| `notifications` | 🔔 | General notifications / reminders |
| `warning` | ⚠️ | Critical alarms, water leaks, faults |
| `info` | ℹ️ | Informational notices |
| `home` | 🏠 | Presence, welcome home, doorbell |
| `thermometer` | 🌡️ | Temperature alerts / climate |
| `window` | 🪟 | Window / door left open |
| `lightbulb` | 💡 | Lighting automation alerts |
| `lock` / `lock_open`| 🔒 / 🔓 | Door locks, alarm armed/disarmed |
| `sentiment_satisfied` | 😊 | Friendly greetings, tasks finished |
| `delete` | 🗑️ | Bin collection reminders |

#### Supported Colors
Accepts common color names (`white`, `cyan`, `green`, `yellow`, `orange`, `red`, `magenta`, `blue`), hex strings (`#FF9900`, `0xFF9900`), or raw hex (`FF9900`).

---

### 2. Playing TTS Voice Announcements
Send speech announcements directly through the internal speaker:

```yaml
action: tts.speak
target:
  entity_id: tts.google_en_com # or tts.piper / cloud TTS
data:
  media_player_entity_id: media_player.minidisplay_speaker
  message: "Laundry cycle is complete."
```

---

## 👆 Touch Gestures

```
                    ┌─────────────────────────┐
                    │    CLOCK & WEATHER      │
                    │         (Home)          │
                    └────────────┬────────────┘
                                 │
                     Swipe Down  │  Swipe Up
                                 ▼
                    ┌─────────────────────────┐
                    │   NOTIFICATIONS QUEUE   │◄──── Swipe Left / Right
                    │         (LIFO)          │      (Browse History)
                    └────────────┬────────────┘
                                 │
                     Swipe Down  │  Button "NO" (Cancel)
                                 ▼
                    ┌─────────────────────────┐
                    │  CLEAR ALL NOTIFICATIONS│
                    │   (Confirmation Modal)  │
                    └─────────────────────────┘
```

* **Double Tap (Anywhere)**: Toggle screen backlight On / Off.
* **Long Press (On Clock)**: Open system diagnostics page (IP, Wi-Fi RSSI, battery voltage, uptime).

---

## 📁 Repository Structure

```
.
├── minidisplay.yaml            # Modular entrypoint (with substitutions)
├── minidisplay_standalone.yaml # Standalone all-in-one file (single-file install)
├── secrets.yaml.example        # Credentials template
├── packages/                   # Clean modular packages
│   ├── core.yaml               # ESP32-S3, PSRAM, Wi-Fi, OTA, SNTP time
│   ├── display.yaml            # GC9A01A, CST816D, Backlight PWM, sleep timer
│   ├── audio.yaml              # ES8311, I2S, Dynamic amp control, Media Player
│   ├── sensors.yaml            # Battery ADC, charging sensor, Wi-Fi diagnostic
│   ├── fonts.yaml              # Roboto & Material Symbols typography
│   ├── notifications.yaml      # Notification queue & API action
│   ├── weather.yaml            # Home Assistant weather synchronization
│   └── ui.yaml                 # LVGL pages, widgets, and gesture handlers
├── examples/home-assistant/    # Ready-to-use Home Assistant automations & scripts
│   ├── automations.yaml        # Doorbell, laundry, temperature, weather automations
│   └── script_send_notification.yaml # Reusable notification script with UI fields
├── blueprint/                  # Home Assistant visual blueprint
│   └── minidisplay_notification.yaml
├── docs/
│   ├── QUICKSTART.md           # 3-step rapid setup guide
│   └── HARDWARE.md             # Reverse-engineered pinout, schematics & backup guide
└── LICENSE                     # MIT License
```

---

## 🤝 Credits & Acknowledgements

* Reverse engineering hardware insights from [78/xiaozhi-esp32](https://github.com/78/xiaozhi-esp32).
* Built with [ESPHome](https://esphome.io) and [LVGL](https://lvgl.io).
* Created with ❤️ by **pasmimmo** for the Home Assistant & ESPHome community.

