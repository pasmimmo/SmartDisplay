# 🏠 Home Assistant Integration Guide

---

## 1. Visual Automation Blueprint (1-Click Import)

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fpasmimmo%2FSmartDisplay%2Fblob%2Fmain%2Fblueprint%2Fsmartdisplay_notification.yaml)

To import into Home Assistant manually:
1. Go to **Settings** > **Automations & Scenes** > **Blueprints**.
2. Click **Import Blueprint** (bottom-right).
3. Paste:
   ```
   https://github.com/pasmimmo/SmartDisplay/blob/main/blueprint/smartdisplay_notification.yaml
   ```
4. Click **Preview Blueprint** and confirm **Import**.

---

## 2. Notification Action Reference

Call `esphome.smartdisplay_send_notification` from any automation or script:

```yaml
action: esphome.smartdisplay_send_notification
data:
  message: "Front door was unlocked!"
  icon: "lock_open"
  text_color: "orange"
  timeout: 30
```

### Supported Notification Icons
* `notifications`: 🔔 Bell
* `warning`: ⚠️ Alarm / Fault
* `info`: ℹ️ Notice
* `home`: 🏠 Presence / Doorbell
* `thermometer`: 🌡️ Climate
* `window`: 🪟 Door / Window open
* `lightbulb`: 💡 Lighting
* `lock` / `lock_open`: 🔒 / 🔓 Locks
* `sentiment_satisfied`: 😊 Greetings / Tasks done
* `delete`: 🗑️ Bin collection

### Supported Colors
`white`, `cyan`, `green`, `yellow`, `orange`, `red`, `magenta`, `blue`, or any hex string (`#FF9900`).

---

## 3. Playing TTS Voice Announcements

```yaml
action: tts.speak
target:
  entity_id: tts.google_en_com # or tts.piper / cloud TTS
data:
  media_player_entity_id: media_player.smartdisplay_speaker
  message: "Washing machine cycle complete."
```

---

## 4. Backlight & Dimming Control
Controlled via **`light.smartdisplay_backlight`** with a 0%–100% brightness slider in Home Assistant.
Double-tapping the screen toggles the backlight On / Off.
