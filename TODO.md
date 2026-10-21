# 📋 MiniDisplay ESP32-S3 - Piano di Lavoro & TODO

Progetto firmware open-source basato su ESPHome e LVGL per smart display circolare ESP32-S3 1.28" (SP-ESP32-S3-1.28-BOX) integrato con Home Assistant.

---

## 🧭 Fasi del Progetto

```
[Fase 1: Riorganizzazione Codice] ──> [Fase 2: Test & Fix] ──> [Fase 3: Nuove Migliorie]
                                                                        │
[Fase 5: Documentazione Doppia]  <── [Fase 4: Esempi Automazioni] <─────┘
           │
           └───> [Fase 6: Estensioni Future (HA Custom Component / UI)]
```

---

## 📌 Checklist Dettagliata

### Fase 1: Riorganizzazione del Codice (Modularizzazione & Best Practice)
- [x] Inizializzare repository Git locale (`git init`)
- [x] Creare `.gitignore` ottimizzato per ESPHome (`.esphome/`, build files, `secrets.yaml`, ecc.)
- [x] Creare `secrets.yaml.example` con istruzioni in inglese
- [x] Progettare e implementare architettura modulare a `packages/`:
  - [x] `packages/core.yaml`: ESP32-S3, PSRAM octal, logging, OTA, Wi-Fi base, time SNTP, BLE passivo
  - [x] `packages/display.yaml`: Bus SPI, display GC9A01A, touchscreen CST816D, backlight LEDC, script timer spegnimento e toggle
  - [x] `packages/audio.yaml`: Bus I2C/I2S, DAC ES8311, amplificatore GPIO46, media_player per TTS
  - [x] `packages/sensors.yaml`: ADC batteria GPIO1 con filtro median e attributi completi Home Assistant, sensore carica GPIO41, diagnostica
  - [x] `packages/fonts.yaml`: Font Roboto e icone Material Symbols Outlined (notifiche e meteo)
  - [x] `packages/notifications.yaml`: Coda LIFO, HA API action `send_notification`, parser colori pulito, timeout
  - [x] `packages/weather.yaml`: Integrazione meteo HA con icone e temperature
  - [x] `packages/ui.yaml`: Pagine Home (orologio + meteo), Notifiche, Debug, Dialog cancellazione, Transizione
- [x] Refactoring generale: commenti in inglese in tutti i file, rimozione ridondanze, riordino degli script di gestione display
- [x] Sincronizzazione continua della copia monolitica all-in-one (`minidisplay_standalone.yaml`)

---

### Fase 2: Test & Fix di Problemi Noti
- [x] **Fix colori pulsanti dialog cancellazione notifiche**: Invertiti colori (NO = Verde `0x009900` per annullare/mantenere, SI = Rosso `0xCC0000` per cancellazione distruttiva) sia nella versione modulare che monolitica
- [ ] Test su hardware fisico (flash tramite ESPHome e verifica funzionamento di tutti i moduli)
- [ ] Verifica calibrazione batteria (GPIO1 ADC 3.3V - 4.2V, stabilità campionamento)
- [ ] Verifica sensore di ricarica (GPIO41)
- [ ] Verifica audio/TTS ed eventuale rumore di fondo all'attivazione dell'amplificatore GPIO46

---

### Fase 3: Migliorie & Nuove Funzionalità
- [x] **Meteo in standby**: Mostrare temperatura e icona meteo dinamica nella `home_page` oltre all'orologio
  - [x] Integrata entità `weather.forecast_casa` tramite `packages/weather.yaml`
  - [x] Aggiunti simboli meteo (`sunny`, `rainy`, `cloudy`, `thunderstorm`, `snow`, `fog`, `night`) in `packages/fonts.yaml`
  - [x] Adattato layout circolare: orologio a `y: -20`, icona meteo (`x: -28`, `y: 44`) e temperatura (`x: 18`, `y: 44`)
  - [x] Aggiornato sia il codice modulare sia `minidisplay_standalone.yaml`
- [x] **Ottimizzazione A - Orologio a evento**: Aggiornamento al secondo esatto (`on_time: seconds: 0` + `on_time_sync` + `on_load`), rimosso polling continuo ogni 10s
- [x] **Ottimizzazione B - Dimming & Transizioni**: Frequenza PWM 1kHz per dimming senza sfarfallio, transizioni morbide a dissolvenza (fade out 500ms allo standby, 250ms al risveglio)
- [x] **Ottimizzazione C - Badge Notifiche non lette**: Campanella d'avviso ambra sopra l'orologio (`y: -65`) che appare automaticamente quando ci sono notifiche nella coda e scompare quando vengono cancellate
- [x] **Ottimizzazione D - Risparmio Batteria Audio**: Amplificatore GPIO46 gestito dinamicamente (acceso solo durante annunci/riproduzione, spento con delay 1s in standby per eliminare fruscio e risparmiare ~20mA)

---

### Fase 4: Esempi di Utilizzo & Automazioni Home Assistant
- [x] Creare cartella `examples/home-assistant/`
- [x] Automazioni d'uso reale in [`examples/home-assistant/automations.yaml`](examples/home-assistant/automations.yaml) (campanello con TTS, fine lavatrice, allarme alta temperatura, finestra aperta con pioggia)
- [x] Script riutilizzabile con interfaccia e campi formattati in [`examples/home-assistant/script_send_notification.yaml`](examples/home-assistant/script_send_notification.yaml)
- [x] Blueprint visuale per Home Assistant in [`blueprint/minidisplay_notification.yaml`](blueprint/minidisplay_notification.yaml)

---

### Fase 5: Documentazione Doppia (Lapidaria & Tecnica)
- [x] **Guida Lapidaria / Quickstart in 3 passi** in [`docs/QUICKSTART.md`](docs/QUICKSTART.md):
  - Compra il device -> Inserisci Wi-Fi -> Flash -> Invia notifica
- [x] **Documentazione Tecnica Completa** in [`docs/HARDWARE.md`](docs/HARDWARE.md):
  - Riconoscimento hardware `SP-ESP32-S3-1.28-BOX` e link AliExpress
  - Riferimento al firmware stock cinese `78/xiaozhi-esp32`
  - Tabella pinout completa a 19 GPIO (SPI, I2C, I2S, ADC, PWM, power)
  - Architettura di gestione energetica e calibrazione batteria
  - Istruzioni di backup e ripristino firmware di serie con `esptool.py`
- [x] **README.md principale di livello GitHub**:
  - Badge professionali, panoramica, tabella icone e colori, diagramma delle gesture touch, albero del repository
- [x] Licenza MIT in [`LICENSE`](LICENSE)

---

### Fase 6: Estensioni Future
- [ ] Custom Component / Lovelace Card guidata per Home Assistant
