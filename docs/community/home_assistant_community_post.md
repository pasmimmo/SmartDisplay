# 🔮 SmartDisplay: Repurposing the 1.28" ESP32-S3 Circular Box into a 100% Local Home Assistant Notification Display & TTS Speaker (ESPHome + LVGL 8)

**Category:** *Share your Projects!*  
**Tags:** `esphome`, `esp32-s3`, `lvgl`, `bluetooth-proxy`, `smart-display`, `blueprints`

---

Hey everyone! 👋

I wanted to share a project I've been working on to liberate the cheap AliExpress circular desktop companion (often sold as the **"Xiaozhi AI Desktop Bot"** or **`SP-ESP32-S3-1.28-BOX`**) and turn it into a **100% local, private, beautifully integrated Home Assistant desktop smart display**.

Originally, this little gadget comes with cloud-locked Chinese firmware that connects to external AI services. But under the hood, the hardware is a little beast:
* **ESP32-S3** with **16MB Flash** and **8MB Octal PSRAM**
* **1.28" Circular IPS LCD** (240x240, GC9A01A driver)
* **CST816D Capacitive Touchscreen**
* **Everest ES8311 I2S Audio DAC** + onboard Class-D speaker amplifier
* **Rechargeable 1S LiPo battery** + USB-C charging circuit

---

## 📸 Real Hardware Showcase

Here is how it looks sitting on my desk running the open-source firmware:

| 🕒 Standby Clock & Weather | 📬 Notification Card |
| :---: | :---: |
| ![Standby Clock](https://raw.githubusercontent.com/pasmimmo/SmartDisplay/main/.github/assets/smartdisplay_standby_clock.jpg) | ![Notification Card](https://raw.githubusercontent.com/pasmimmo/SmartDisplay/main/.github/assets/smartdisplay_notification_card.jpg) |
| **🗑️ Modal Confirmation** | **⚙️ System Diagnostics** |
| ![Modal Dialog](https://raw.githubusercontent.com/pasmimmo/SmartDisplay/main/.github/assets/smartdisplay_clear_dialog.jpg) | ![System Diagnostics](https://raw.githubusercontent.com/pasmimmo/SmartDisplay/main/.github/assets/smartdisplay_system_info.jpg) |

---

## 🚀 Key Features

### 1. 🎨 LittlevGL 8 Circular UI
* The SPI bus is boosted to **80 MHz** with DMA, achieving buttery-smooth 30 FPS animations and page transitions.
* **Event-driven clock**: Slices CPU wake-locks by synchronizing exactly on the minute change and connection events, saving battery and keeping the chip cool.
* **Dynamic Weather Widget**: Pulls outdoor temperature and condition icons directly from Home Assistant's `weather` entity, with dynamic thematic colors (sunny amber, rain blue, night sky blue, etc.).
* **Unread Notification Badge**: A discreet amber bell badge appears above the clock face whenever alerts are sitting in the queue.

### 2. 📬 LIFO 10-Item Notification Queue
* Stores up to 10 incoming alerts in a carousel ring-buffer.
* Prioritizes recent alerts (Last-In, First-Out), so critical alarms (doorbell, water leak, security) instantly pop up on top of older messages.
* Interactive touch gestures: Swipe down to inspect notifications, swipe left/right to browse history, swipe down again to open a clear-all modal, or double-tap anywhere to sleep/wake the screen.

### 3. 🔊 Spoken Announcements & Dynamic Audio Power Gating
* Fully integrated with Home Assistant's `tts.speak` and `announcement_pipeline`.
* **Hardware Power Gating (`GPIO46`)**: The onboard NS4168 Class-D amplifier is dynamically cut when idle. This completely eliminates high-frequency background speaker hiss and saves $\approx 20\text{ mA}$ quiescent current.

### 4. 📡 Passive Bluetooth Proxy (Xiaomi Mi Flora Plant Care)
* Configured as a **passive BLE proxy** (`active: false`).
* Because it's passive, there is zero 2.4 GHz radio contention with Wi-Fi, preventing TTS audio stuttering or dropped HA API packets.
* Perfect for picking up nearby **Xiaomi Mi Flora / Flower Care plant sensors**, BTHome beacons, or BLE thermometers and streaming telemetry to Home Assistant.

### 5. 🎛️ Night Dimmer Light Entity
* Backlight PWM on `GPIO42` is exposed as a dimmable light entity: `light.smartdisplay_backlight`.
* Automations can smoothly dim the screen down to 10-20% at night and bring it back up to 100% in the morning.

---

## ⚡ 1-Click Visual Blueprint Import

I built a Home Assistant Automation Blueprint so you don't even need to write YAML to trigger notifications:

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fpasmimmo%2FSmartDisplay%2Fblob%2Fmain%2Fblueprint%2Fsmartdisplay_notification.yaml)

Simply pick your trigger entity, type your message, and select your icon and accent color from the dropdowns!

---

## 🛠️ Repository & Architecture Documentation

The project includes both a clean **modular package architecture** (`packages/*.yaml`) and an **all-in-one single-file configuration** (`smartdisplay_standalone.yaml`):

* 🐙 **GitHub Repository:** [pasmimmo/SmartDisplay](https://github.com/pasmimmo/SmartDisplay)
* 📖 **Official GitHub Wiki:** [SmartDisplay Wiki](https://github.com/pasmimmo/SmartDisplay/wiki)
* 🧠 **Architecture Decision Records (ADR):** [docs/ARCHITECTURE_DECISIONS.md](https://github.com/pasmimmo/SmartDisplay/blob/main/docs/ARCHITECTURE_DECISIONS.md)

Feedback, feature ideas, and pull requests are warmly welcomed! Let me know what you think! 🚀

