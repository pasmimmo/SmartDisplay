# 🔮 Welcome to the SmartDisplay Wiki

**SmartDisplay** is an open-source, modular firmware based on **ESPHome** and **LVGL 8** designed for the **SP-ESP32-S3-1.28-BOX** circular smart display.

It repurposes the popular AliExpress [Xiaozhi AI desktop box](https://it.aliexpress.com/item/1005008821384965.html) into a **100% local, private, full-featured Home Assistant notification display, desktop clock, weather widget, and TTS voice announcement speaker**.

---

## 📸 Real Hardware Showcase

<p align="center">
  <img src="https://raw.githubusercontent.com/pasmimmo/SmartDisplay/main/.github/assets/smartdisplay_standby_clock.jpg" width="22%" alt="Standby Clock" />
  <img src="https://raw.githubusercontent.com/pasmimmo/SmartDisplay/main/.github/assets/smartdisplay_notification_card.jpg" width="22%" alt="Notification Card" />
  <img src="https://raw.githubusercontent.com/pasmimmo/SmartDisplay/main/.github/assets/smartdisplay_clear_dialog.jpg" width="22%" alt="Modal Confirmation" />
  <img src="https://raw.githubusercontent.com/pasmimmo/SmartDisplay/main/.github/assets/smartdisplay_system_info.jpg" width="22%" alt="System Diagnostics" />
</p>

| 🕒 Standby Clock & Weather | 📬 Notification Card | 🗑️ Modal Confirmation | ⚙️ System Diagnostics |
| :---: | :---: | :---: | :---: |
| Event-driven clock with live rain/temp and unread alert badge | Preemptive LIFO alert with Material Symbol icon | Destructive action confirmation with color-coded safety buttons | IP address, Wi-Fi RSSI (dBm), battery gauge & uptime |

---

## 🧭 Wiki Navigation

* **[⚡ Quickstart Guide](Quickstart)** — How to configure and flash your SmartDisplay in under 3 minutes.
* **[🔬 Hardware & Pinout Reference](Hardware-and-Pinout)** — Complete 19-GPIO table, power circuitry, board revisions warning, and stock firmware backup.
* **[🏠 Home Assistant Integration](Home-Assistant-Integration)** — Action calls, automations, visual blueprint, and TTS announcements.
* **[📡 Bluetooth Proxy & Plant Care](Bluetooth-Proxy)** — How to monitor Xiaomi Mi Flora sensors and BTHome beacons with passive BLE.
* **[❓ Troubleshooting & FAQ](Troubleshooting)** — Common issues, Wi-Fi recovery, and circular UI clipping tips.

---

## 🗺️ System Architecture

```mermaid
flowchart LR
    HA["🏠 <b>Home Assistant</b>"]
    SD["🔮 <b>Smart Display</b><br><i>(ESP32-S3)</i>"]
    BLE["📡 <b>BLE Devices</b><br><i>(Sensors & Beacons)</i>"]

    HA -->|"🔔 Notifications & Alerts"| SD
    HA -->|"⛅ Weather & Time Sync"| SD
    HA -->|"🔊 TTS Voice Stream"| SD
    SD -->|"📶 Proxied BLE Telemetry"| HA
    SD -->|"🔋 Battery & Diagnostics"| HA

    BLE -->|"📡 Beacons & Sensor Data<br><i>(Xiaomi Flora, BTHome)</i>"| SD
```

---

## 🛒 Hardware Compatibility

| Component | Specification |
| :--- | :--- |
| **SoC** | Espressif ESP32-S3 (Dual-core 240MHz, 16MB Flash, 8MB Octal PSRAM) |
| **Display** | 1.28-inch circular IPS LCD (240x240, GC9A01A over 80MHz SPI) |
| **Touch** | CST816D capacitive touch controller (I2C) |
| **Audio** | Everest ES8311 DAC + onboard speaker with GPIO46 power gating |
| **Power** | 1S LiPo battery + USB-C charging + GPIO1 battery ADC + GPIO41 charging sensor |
