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
- [x] Creare `secrets.yaml.example` con placeholder puliti
- [x] Progettare e implementare architettura modulare a `packages/`:
  - [x] `packages/core.yaml`: ESP32-S3, PSRAM octal, logging, OTA, Wi-Fi base, time SNTP, BLE
  - [x] `packages/display.yaml`: Bus SPI, display GC9A01A, touchscreen CST816D, backlight LEDC
  - [x] `packages/audio.yaml`: Bus I2C/I2S, DAC ES8311, amplificatore GPIO46, media_player
  - [x] `packages/sensors.yaml`: ADC batteria GPIO1, sensore carica GPIO41, diagnostica (uptime, wifi)
  - [x] `packages/fonts.yaml`: Font Roboto e icone Material Symbols Outlined
  - [x] `packages/notifications.yaml`: Logica coda notifiche LIFO, HA API action `send_notification`, timeout
  - [x] `packages/ui.yaml`: Pagine Home (orologio), Notifiche, Debug, Dialog cancellazione, Transizione
- [x] Creare il file di configurazione master compatto (`minidisplay.yaml`) con `substitutions:` e inclusioni
- [x] Mantenere copia monolitica all-in-one (`minidisplay_standalone.yaml`) per chi preferisce un unico file

---

### Fase 2: Test & Fix di Problemi Noti
- [x] **Fix colori pulsanti dialog cancellazione notifiche**: Invertiti colori (NO = Verde `0x009900` per annullare/mantenere, SI = Rosso `0xCC0000` per cancellazione distruttiva) sia nella versione modulare che monolitica
- [ ] Test su hardware fisico (flash tramite ESPHome e verifica funzionamento di tutti i moduli)
- [ ] Verifica calibrazione batteria (GPIO1 ADC 3.3V - 4.2V, stabilità campionamento)
- [ ] Verifica sensore di ricarica (GPIO41)
- [ ] Verifica audio/TTS ed eventuale rumore di fondo all'attivazione dell'amplificatore GPIO46

---

### Fase 3: Migliorie & Nuove Funzionalità
- [ ] **Meteo in standby**: Mostrare temperatura e icona meteo (o stato sintetico) nella `home_page` oltre all'orologio
  - [ ] Aggiungere entità Home Assistant (`homeassistant` sensor/text_sensor per temperatura e meteo)
  - [ ] Aggiungere icone meteo al font Material Symbols (sole, nuvoloso, pioggia, neve, temporale)
  - [ ] Riorganizzare il layout circolare di `home_page` per accogliere ora + meteo in modo armonioso
- [ ] Controllo luminosità dimmerabile da Home Assistant o modalità notturna automatica
- [ ] Segnale sonoro / beep opzionale alla ricezione di una notifica (tramite speaker I2S)
- [ ] Indicatore visuale rapido (es. piccolo badge/pallino se ci sono notifiche non lette)

---

### Fase 4: Esempi di Utilizzo & Automazioni Home Assistant
- [ ] Creare cartella `examples/home-assistant/`
- [ ] Automazioni d'uso reale (lavatrice/asciugatrice, campanello, allarme perimetrale, meteo)
- [ ] Script riutilizzabile HA con validazione parametri
- [ ] Blueprint per Home Assistant

---

### Fase 5: Documentazione Doppia (Lapidaria & Tecnica)
- [ ] **Guida Lapidaria / Quickstart (`docs/QUICKSTART.md`)**:
  - Compra il device -> Inserisci Wi-Fi -> Flash -> Usa automazione
- [ ] **Documentazione Tecnica Completa (`docs/HARDWARE.md` & `README.md`)**:
  - Foto e link AliExpress del modello `SP-ESP32-S3-1.28-BOX`
  - Link alla repo del firmware originale di serie
  - Tabella pinout completa (GC9A01A, CST816D, ES8311, ADC, GPIO)
  - Tabella gesture ed elenco icone supportate
  - Istruzioni di flashing & ripristino
- [ ] Licenza (MIT)

---

### Fase 6: Estensioni Future
- [ ] Custom Component / Lovelace Card guidata per Home Assistant
