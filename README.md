# 🔮 MiniDisplay ESP32-S3

[![ESPHome Version](https://img.shields.io/badge/ESPHome-2024.10%2B-blue.svg)](https://esphome.io)
[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Compatible-41BDF5.svg)](https://home-assistant.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Hardware](https://img.shields.io/badge/Hardware-SP--ESP32--S3--1.28--BOX-orange.svg)](https://it.aliexpress.com/item/1005008821384965.html)

**MiniDisplay** is a modern, modular, open-source firmware based on **ESPHome** and **LVGL 8** designed for the **SP-ESP32-S3-1.28-BOX** circular smart display. 

It repurposes the popular AliExpress [Xiaozhi AI desktop box](https://it.aliexpress.com/item/1005008821384965.html) (originally running cloud-locked firmware from [78/xiaozhi-esp32](https://github.com/78/xiaozhi-esp32)) into a **100% local, private, full-featured Home Assistant notification display, desktop clock, weather widget, and TTS voice announcement speaker**.

---

## 🗺️ System Architecture & Workflow

Here is how the entire ecosystem connects at a glance — from Home Assistant automations down to the ESP32-S3 hardware and passive Bluetooth sensors:

```mermaid
flowchart TD
    subgraph HA["🏠 Home Assistant Ecosystem"]
        HA_Auto["Automations & Scripts<br>(Doorbell, Appliances, Leaks)"]
        HA_Weather["Weather Entity<br>(weather.home)"]
        HA_TTS["TTS Engine / Audio<br>(Piper / Cloud Voice)"]
        HA_BLE["Bluetooth Integration<br>(Xiaomi Mi Flora / BTHome)"]
    end

    subgraph ESP["🔮 MiniDisplay (ESP32-S3 Firmware)"]
        direction TB
        API["ESPHome Native API & SNTP Engine"]
        Queue["LIFO Notification Engine<br>(10-Card Memory Queue)"]
        LVGL["LVGL 8 Graphics Engine<br>(Clock, Weather, Notifs, Debug)"]
        Audio["Audio Pipeline & DAC<br>(GPIO46 Dynamic Amp Gating)"]
        BLE["Passive BLE Proxy<br>(Zero audio/Wi-Fi stuttering)"]
        Power["Power & Battery Manager<br>(ADC Battery, USB-C detect)"]
    end

    subgraph HW["📱 Hardware (SP-ESP32-S3-1.28-BOX)"]
        LCD["1.28' Round IPS LCD<br>(GC9A01A @ 80MHz SPI)"]
        Touch["Capacitive Touchscreen<br>(CST816D I2C)"]
        Speaker["Internal Speaker & Amp<br>(Everest ES8311 DAC)"]
        Battery["1S LiPo + USB-C Charger<br>(GPIO1 ADC / GPIO41 VBUS)"]
    end

    subgraph Sensors["🌿 Local BLE Peripherals"]
        Flora["Xiaomi Mi Flora Sensors<br>(Soil moisture, sunlight, temp)"]
        Beacons["BTHome Thermometers & Beacons"]
    end

    %% Flow connections
    HA_Auto -->|"esphome.minidisplay_send_notification"| API
    HA_Weather -->|"Live conditions & temperature"| API
    HA_TTS -->|"Voice Stream (I2S)"| Audio
    Sensors -->|"Passive BLE Advertisements"| BLE
    BLE -->|"Forwarded sensor data"| HA_BLE

    API --> Queue
    API --> LVGL
    Queue --> LVGL

    LVGL -->|"Flicker-free 1kHz PWM + SPI"| LCD
    Touch -->|"Swipes, taps & long-press"| LVGL
    Audio -->|"Audio output + GPIO46 auto-enable"| Speaker
    Battery -->|"Battery level & charging status"| Power
    Power -->|"Smart auto-sleep (battery only)"| LVGL
```

### 🔄 Event & Lifecycle Flow

```mermaid
sequenceDiagram
    autonumber
    actor User as 👤 User
    participant HA as 🏠 Home Assistant
    participant ESP as 🔮 MiniDisplay (ESP32-S3)
    participant LCD as 🎨 1.28" Round Screen (LVGL)
    participant Audio as 🔊 Speaker (ES8311)

    Note over HA,ESP: 1. Idle Standby (Desk Mode)
    HA->>ESP: Sync SNTP Time & Live Weather
    ESP->>LCD: Display Clock + Outdoor Weather & Temp

    rect rgb(30, 42, 56)
    Note over HA,ESP: 2. Notification Trigger (e.g., Washing Machine / Doorbell)
    HA->>ESP: Call esphome.minidisplay_send_notification
    par Visual Alert
        ESP->>LCD: Wake display / Fade on backlight (LEDC PWM)
        ESP->>LCD: Show icon, colored text & counter (e.g. 1/3)
    and Voice Announcement (Optional)
        HA->>Audio: Stream TTS voice chime / speech (GPIO46 Amp On)
        Audio-->>Audio: Auto-cutoff amp 1000ms after speech (GPIO46 Amp Off)
    end
    end

    Note over User,LCD: 3. Touch Interaction
    User->>LCD: Swipe Left / Right: Browse 10-card history
    User->>LCD: Swipe Down: Open confirmation dialog to clear all
    User->>LCD: Long Press (2s): Open circular System Diagnostics page
    User->>LCD: Double Tap: Toggle screen backlight sleep/wake
```

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
* **🎛️ Dimmable PWM Backlight**: 1kHz flicker-free LEDC control with smooth fade-in/fade-out transitions. Exposes a 1%–100% brightness slider in Home Assistant to easily dim the screen at night.
* **📡 Passive Bluetooth Proxy (Xiaomi Plant Sensors)**: Acts as a localized BLE proxy for Home Assistant. Ideal for capturing passive data from **Xiaomi Mi Flora / Flower Care plant sensors**, BTHome beacons, or BLE thermometers without connection overhead or audio stuttering.
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

> [!WARNING]
> **Hardware Revisions & Pinout Notice:** AliExpress vendors occasionally release revised PCB revisions or clones with slightly different GPIO pin assignments (e.g., audio amp enable or touch interrupt). The pinout documented here is verified for the **SP-ESP32-S3-1.28-BOX**. Always inspect your board's silkscreen markings or verify with your seller's schematic if a peripheral doesn't respond!

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

### 3. Standby Weather Widget Setup
The standby clock screen features a live outdoor weather widget displaying your local condition icon and temperature.

#### Configuring Your Weather Entity
In `minidisplay.yaml` (or `minidisplay_standalone.yaml`), simply set `weather_entity` in `substitutions:` to match your Home Assistant weather entity ID:

```yaml
substitutions:
  # Replace with your actual Home Assistant weather entity (e.g. weather.home, weather.forecast_home)
  weather_entity: "weather.forecast_casa"
```

#### Weather Conditions & Dynamic Thematic Colors
The firmware automatically maps Home Assistant weather states to matching Google Material Symbols and accent colors:

| Home Assistant Weather State | Icon | Theme Accent Color | Visual Representation |
| :--- | :---: | :--- | :--- |
| `sunny` | `\uE81A` | Warm Amber (`#FFB300`) | ☀️ Sunny / Clear Day |
| `clear-night` | `\uF159` | Night Sky Blue (`#80D8FF`) | 🌙 Clear Night Sky |
| `partlycloudy` | `\uF172` | Golden Yellow (`#FFCA28`) | ⛅ Sun behind clouds |
| `cloudy` | `\uF15C` | Soft Grey (`#B0BEC5`) | ☁️ Overcast clouds |
| `rainy`, `pouring` | `\uF176` | Rain Blue (`#42A5F5`) | 🌧️ Rain showers |
| `lightning`, `lightning-rainy` | `\uEBDB` | Vivid Yellow (`#FFD54F`) | ⛈️ Thunderstorm |
| `snowy`, `snowy-rainy`, `hail` | `\uEB3B` | Ice Cyan (`#E0F7FA`) | ❄️ Snow / Ice |
| `fog` | `\uE818` | Mist Grey (`#90A4AE`) | 🌫️ Fog & Mist |
| `windy`, `windy-variant` | `\uF15C` | Slate Grey (`#B0BEC5`) | 💨 High winds |

> [!TIP]
> **Offline / Safe Fallback:** If Home Assistant is rebooting, disconnected, or your weather integration is unavailable, the screen automatically defaults to `--°C` with a neutral thermometer icon (`\uE846`), preventing any graphical distortion or crashes.

---

### 4. Screen Brightness & Dimming Control
The display backlight is controlled by a 1kHz PWM signal on `GPIO42`, exposed in Home Assistant as a dimmable light entity: **`light.minidisplay_backlight`**.

* **In Home Assistant UI:** Opening the card for `light.minidisplay_backlight` gives you a **0% to 100% brightness slider**.
* **Night Mode Automations:** You can automatically dim the display at bedtime (e.g. 20% brightness) and brighten it in the morning (100% brightness) using standard Home Assistant automations:
  ```yaml
  action: light.turn_on
  target:
    entity_id: light.minidisplay_backlight
  data:
    brightness_pct: 20
    transition: 2
  ```
* **On-Device Gesture:** Double-tapping anywhere on the screen toggles the backlight On / Off without affecting the configured brightness level.

---

### 5. Bluetooth Proxy: Whole-Home BLE Extension
The firmware integrates ESPHome's **passive Bluetooth Proxy** (`bluetooth_proxy: active: false`), allowing the MiniDisplay to double as a long-range Bluetooth antenna for Home Assistant.

#### Supported Devices & Examples
By placing the MiniDisplay in your living room, bedroom, or office, Home Assistant can automatically discover and communicate with nearby Bluetooth smart devices without requiring expensive dedicated hubs:
* **💡 Smart Lighting & Bulbs:** Philips Hue BLE lamps, Yeelight BLE bulbs, and Tuya Bluetooth lights.
* **🌡️ Climate & Room Sensors:** Xiaomi Mijia E-Ink thermometers (`LYWSD03MMC`), Qingping Air Monitors, ThermoPro BLE sensors, and BTHome beacons.
* **🚪 Security & Motion:** Shelly BLU door/window contacts, Shelly BLU motion detectors, and SwitchBot curtain / bot motors.
* **🏷️ Presence & Proximity:** iBeacon keychains and smartwatch proximity beacons for room presence tracking.

#### 🌱 Author's Real-World Setup: Xiaomi Mi Flora Plant Care
> The author specifically built this setup to monitor house plants in the room!
> 
> * **The Problem:** **Xiaomi Mi Flora (Flower Care)** soil moisture and fertility probes broadcast telemetry using Bluetooth Low Energy (BLE), but their small antennas often fail to reach the main Home Assistant server across multiple walls.
> * **The Solution:** Sitting on a desk or plant stand, the MiniDisplay passively captures the BLE broadcast packets (soil moisture, fertilizer conductivity, sunlight lux, and temperature) and seamlessly forwards them to Home Assistant over Wi-Fi.
> * **Why Passive Mode?** Active Bluetooth pairing consumes heavy RAM and can cause audio stuttering. Passive mode (`active: false`) captures 100% of the plant telemetry without establishing connections, keeping TTS audio crystal-clear while ensuring your plants never dry out!
> * **Full Circle Automation:** When soil moisture drops below 20%, Home Assistant automatically triggers a notification card directly on the MiniDisplay:
>   ```yaml
>   action: esphome.minidisplay_send_notification
>   data:
>     message: "Ficus lyrata needs water! Soil moisture is at 15%."
>     icon: "home"
>     text_color: "green"
>     timeout: 60
>   ```

---

## 👆 Touch Gestures

```mermaid
stateDiagram-v2
    direction TB
    [*] --> Standby_Clock: Device Boot
    
    Standby_Clock --> Notifications_Queue: Swipe Down / New Notification
    Standby_Clock --> Diagnostics_Page: Long Press (2s)
    
    Diagnostics_Page --> Standby_Clock: Swipe / Tap to Exit

    state Notifications_Queue {
        Card_1: Card 1 (Most Recent)
        Card_2: Card 2 (Previous)
        Card_N: Card N (Oldest, up to 10)
        Card_1 --> Card_2: Swipe Left
        Card_2 --> Card_1: Swipe Right
        Card_2 --> Card_N: Swipe Left
    }

    Notifications_Queue --> Standby_Clock: Swipe Up
    Notifications_Queue --> Clear_Modal: Swipe Down
    
    Clear_Modal --> Notifications_Queue: Tap "NO" (Cancel)
    Clear_Modal --> Standby_Clock: Tap "SI" (Clear All)
```

| Gesture | Screen | Action |
| :--- | :--- | :--- |
| **Swipe Down** | Standby Clock | Open latest notification card |
| **Swipe Up** | Notifications | Return to Standby Clock |
| **Swipe Left / Right** | Notifications | Browse 10-card notification history |
| **Swipe Down** | Notifications | Open confirmation modal to clear all notifications |
| **Long Press (2s)** | Standby Clock | Open circular System Diagnostics screen |
| **Double Tap** | Anywhere | Toggle display backlight sleep / wake |

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

## 💡 Have Different Needs? (Fork & Contribute!)

> *Got an idea for a custom screen, want different touch gestures, or have unique needs for your smart home?* 🚀
> 
> Feel completely free to **fork or clone this repository**, tinker with the code, and make it your own! Every contribution, pull request, bug report, or idea is warmly welcomed — let's make this little circular display even better together! ❤️

---

## 🤝 Credits & Contributors

* **[pasmimmo](https://github.com/pasmimmo)**: Creator, reverse engineering, and project maintainer.
* **[Antigravity](https://github.com/google-deepmind)** (Google DeepMind): AI pair programming partner, modular architecture, optimizations & documentation.
* **[78/xiaozhi-esp32](https://github.com/78/xiaozhi-esp32)**: Hardware insights from the original open-source AI desktop bot firmware.
* Built with the incredible power of [ESPHome](https://esphome.io) and [LVGL](https://lvgl.io).


