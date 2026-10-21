# ⚡ Quickstart Guide

Get your SmartDisplay flashed and connected to Home Assistant in under 3 minutes.

---

## Step 1: Hardware Checklist
* **Device:** [ESP32-S3 1.28" Round Display Box on AliExpress](https://it.aliexpress.com/item/1005008821384965.html)
* **What's included:** ESP32-S3, 1.28" 240x240 circular IPS LCD, CST816D touch, ES8311 audio DAC, speaker, LiPo battery, and desktop enclosure with USB-C.

---

## Step 2: Flash via ESPHome Dashboard

### Option A: Standalone Single-File (Easiest)
1. Open your **ESPHome Dashboard** in Home Assistant.
2. Click **+ New Device**, name it `smartdisplay`, and skip the wizard.
3. Click **Edit** on the new device card.
4. Replace the entire content with `smartdisplay_standalone.yaml`.
5. Customize the `substitutions:` at the top:
   ```yaml
   substitutions:
     device_name: "smartdisplay"
     friendly_name: "SmartDisplay"
     timezone: "Europe/Rome"
     weather_entity: "weather.forecast_casa"
     sleep_timeout: "300s"
   ```
6. Add your Wi-Fi credentials to `secrets.yaml`:
   ```yaml
   wifi_ssid: "YOUR_WIFI_SSID"
   wifi_password: "YOUR_WIFI_PASSWORD"
   ```
7. Connect the display via USB-C to your PC and click **Install** -> **Plug into this computer** (Chrome / Edge).

### Option B: Modular Packages (Recommended for Git Users)
1. Copy the `packages/` directory and `smartdisplay.yaml` into your `/config/esphome/` folder.
2. Click **Install** in the ESPHome Dashboard.

---

## Step 3: Test Notification
Go to **Developer Tools** -> **Actions** in Home Assistant and run:

```yaml
action: esphome.smartdisplay_send_notification
data:
  message: "Washing machine cycle finished!"
  icon: "home"
  text_color: "cyan"
  timeout: 30
```
