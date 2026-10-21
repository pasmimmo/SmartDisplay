# 📡 Bluetooth Proxy & Plant Care

SmartDisplay includes ESPHome's **passive Bluetooth Proxy** (`bluetooth_proxy: active: false`), turning the device into a localized Bluetooth antenna for Home Assistant.

---

## Why Passive Mode?
* **Active BLE Mode:** Establishes active connections, consuming massive RAM and causing 2.4GHz Wi-Fi/Bluetooth coexistence interference (resulting in stuttering TTS audio).
* **Passive BLE Mode:** Passively listens to broadcast advertisement packets. Captures 100% of telemetry data from beacons and sensors with zero RAM overhead and zero audio stuttering.

---

## Real-World Example: Xiaomi Mi Flora (Plant Care)
The author created this setup specifically to monitor indoor houseplants:

1. **Xiaomi Mi Flora Probes:** Inserted into plant pots, broadcasting soil moisture, fertilizer conductivity, sunlight lux, and temperature over BLE advertisements.
2. **SmartDisplay Proxy:** Sitting on a desk nearby, it captures the weak BLE signals and relays them over Wi-Fi directly to Home Assistant.
3. **Automated Notification:** When soil moisture drops below 20%, Home Assistant pushes a notification card directly to the SmartDisplay:
   ```yaml
   action: esphome.smartdisplay_send_notification
   data:
     message: "Ficus lyrata needs water! Soil moisture is at 15%."
     icon: "home"
     text_color: "green"
     timeout: 60
   ```

---

## Other Supported BLE Peripherals
* **Smart Lighting:** Philips Hue BLE, Yeelight BLE, Tuya BLE bulbs.
* **Climate Sensors:** Xiaomi Mijia E-Ink thermometers (`LYWSD03MMC`), Qingping Air Monitors, ThermoPro BLE sensors, and BTHome beacons.
* **Security & Motion:** Shelly BLU door/window contacts, Shelly BLU motion detectors.
* **Room Presence:** iBeacon keychains and smartwatch proximity beacons.
