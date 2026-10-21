---
title: "SP-ESP32-S3-1.28-BOX Circular Smart Display"
date: 2026-10-21
author: "pasmimmo"
project_url: "https://github.com/pasmimmo/SmartDisplay"
difficulty: 2
category: "Display"
tags:
  - "esp32-s3"
  - "gc9a01a"
  - "cst816d"
  - "es8311"
  - "lvgl"
  - "bluetooth_proxy"
  - "battery"
---

# SP-ESP32-S3-1.28-BOX Circular Smart Display

The **SP-ESP32-S3-1.28-BOX** (popularly sold on AliExpress as the "Xiaozhi AI Desktop Companion") is an all-in-one circular smart display in a spherical desktop enclosure. 

It features an ESP32-S3 SoC with 16MB Flash and 8MB Octal PSRAM, a 1.28" circular IPS LCD (GC9A01A), CST816D capacitive touch screen, Everest ES8311 I2S audio DAC with an onboard Class-D speaker amplifier, a rechargeable 1S LiPo battery, and USB-C connectivity.

## Hardware Specifications

| Peripheral | Chipset / Pins | Details |
| :--- | :--- | :--- |
| **SoC** | Espressif ESP32-S3 | Dual-core XTensa LX7 @ 240MHz, 16MB Flash, 8MB Octal PSRAM |
| **Display** | GC9A01A | 1.28" Circular IPS LCD ($240 \times 240$), 4-wire SPI @ 80MHz |
| **Touchscreen** | CST816D | Capacitive I2C (SDA: `GPIO4`, SCL: `GPIO8`, INT: `GPIO11`, RST: `GPIO12`) |
| **Audio DAC** | Everest ES8311 | I2C config + I2S digital audio (DIN: `GPIO16`, BCLK: `GPIO17`, LRCK: `GPIO18`) |
| **Amplifier** | NS4168 | Mono Class-D, Power Gating / Enable via `GPIO46` |
| **Backlight** | LEDC PWM | 1kHz PWM dimming on `GPIO42` |
| **Battery ADC** | Internal Divider | Voltage divider on `GPIO1` with median filtering |
| **Charging** | Detection Pin | Active-low charging sensor on `GPIO41` |

## Flashing Instructions

1. Connect the device via USB-C to your computer.
2. If the ESP32-S3 is not detected in bootloader mode, hold down the onboard `BOOT` button (accessible via pinhole on the bottom or enclosure) while plugging in the USB-C cable.
3. Flash using ESPHome Web Tools ([web.esphome.io](https://web.esphome.io)) or the ESPHome Dashboard.

## Configuration

You can install the complete community firmware directly from GitHub using ESPHome's `packages` feature:

```yaml
substitutions:
  device_name: "smartdisplay"
  friendly_name: "SmartDisplay"
  timezone: "Europe/Rome"
  weather_entity: "weather.forecast_casa"
  sleep_timeout: "300s"

packages:
  remote_display:
    url: "https://github.com/pasmimmo/SmartDisplay"
    ref: "main"
    files:
      - packages/core.yaml
      - packages/display.yaml
      - packages/audio.yaml
      - packages/sensors.yaml
      - packages/fonts.yaml
      - packages/notifications.yaml
      - packages/weather.yaml
      - packages/ui.yaml
```

Alternatively, download the standalone single-file configuration from [`smartdisplay_standalone.yaml`](https://github.com/pasmimmo/SmartDisplay/blob/main/smartdisplay_standalone.yaml).

## Key Features

* **LittlevGL (LVGL 8) UI**: Smooth circular graphics with event-driven clock, dynamic weather conditions, and touch gesture engine.
* **LIFO Notification Queue**: Interactive 10-item carousel prioritizing immediate glanceability for home alarms.
* **Passive Bluetooth Proxy**: Forwards telemetry from Xiaomi Mi Flora plant monitors, BTHome sensors, and BLE beacons without radio interference.
* **Acoustic Power Gating**: Dynamic power switching on `GPIO46` eliminates Class-D quiescent draw (~20mA) and idle speaker hiss.
