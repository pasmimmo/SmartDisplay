# 📋 SmartDisplay ESP32-S3 - Piano di Lavoro & TODO

Progetto firmware open-source basato su ESPHome e LVGL per smart display circolare ESP32-S3 1.28" (SP-ESP32-S3-1.28-BOX) integrato con Home Assistant.

---

## 🧭 Fasi del Progetto

```
[Fase 1: Riorganizzazione Codice] ──> [Fase 2: Test & Fix] ──> [Fase 3: Nuove Migliorie]
                                                                        │
[Fase 6: Portfolio & Engineering] <── [Fase 5: Documentazione] <────────┘
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
- [x] Sincronizzazione continua della copia monolitica all-in-one (`smartdisplay_standalone.yaml`)

---

### Fase 2: Test & Fix di Problemi Noti
- [x] **Fix colori pulsanti dialog cancellazione notifiche**: Invertiti colori (NO = Verde `0x009900` per annullare/mantenere, SI = Rosso `0xCC0000` per cancellazione distruttiva) sia nella versione modulare che monolitica
- [x] Test su hardware fisico (flash tramite ESPHome e verifica funzionamento di tutti i moduli)
- [x] Verifica calibrazione batteria (GPIO1 ADC 3.3V - 4.2V, stabilità campionamento)
- [x] Verifica sensore di ricarica (GPIO41)
- [x] Verifica audio/TTS ed eventuale rumore di fondo all'attivazione dell'amplificatore GPIO46

---

### Fase 3: Migliorie & Nuove Funzionalità
- [x] **Meteo in standby**: Mostrare temperatura e icona meteo dinamica nella `home_page` oltre all'orologio
  - [x] Integrata entità `weather.forecast_casa` tramite `packages/weather.yaml`
  - [x] Aggiunti simboli meteo (`sunny`, `rainy`, `cloudy`, `thunderstorm`, `snow`, `fog`, `night`) in `packages/fonts.yaml`
  - [x] Adattato layout circolare: orologio a `y: -20`, icona meteo (`x: -28`, `y: 44`) e temperatura (`x: 18`, `y: 44`)
  - [x] Aggiornato sia il codice modulare sia `smartdisplay_standalone.yaml`
- [x] **Ottimizzazione A - Orologio a evento**: Aggiornamento al secondo esatto (`on_time: seconds: 0` + `on_time_sync` + `on_load`), rimosso polling continuo ogni 10s
- [x] **Ottimizzazione B - Dimming & Transizioni**: Frequenza PWM 1kHz per dimming senza sfarfallio, transizioni morbide a dissolvenza (fade out 500ms allo standby, 250ms al risveglio)
- [x] **Ottimizzazione C - Badge Notifiche non lette**: Campanella d'avviso ambra sopra l'orologio (`y: -65`) che appare automaticamente quando ci sono notifiche nella coda e scompare quando vengono cancellate
- [x] **Ottimizzazione D - Risparmio Batteria Audio**: Amplificatore GPIO46 gestito dinamicamente (acceso solo durante annunci/riproduzione, spento con delay 1s in standby per eliminare fruscio e risparmiare ~20mA)
- [x] **Ottimizzazione E - Ritorno automatico alla Home**: Trigger `on_turn_off` su `display_backlight` che riporta sempre a `home_page` quando lo schermo si spegne
- [x] **Ottimizzazione F - Geometria circolare pagina di debug**: Centratura metrica simmetrica per evitare scritte tagliate su schermo rotondo GC9A01A
- [x] **Ridenominazione Globale**: Uniformato il dispositivo, tutte le entità e i file in `smartdisplay` / `SmartDisplay`

---

### Fase 4: Esempi di Utilizzo & Automazioni Home Assistant
- [x] Creare cartella `examples/home-assistant/`
- [x] Automazioni d'uso reale in [`examples/home-assistant/automations.yaml`](examples/home-assistant/automations.yaml) (campanello con TTS, fine lavatrice, allarme alta temperatura, finestra aperta con pioggia)
- [x] Script riutilizzabile con interfaccia e campi formattati in [`examples/home-assistant/script_send_notification.yaml`](examples/home-assistant/script_send_notification.yaml)
- [x] Blueprint visuale per Home Assistant in [`blueprint/smartdisplay_notification.yaml`](blueprint/smartdisplay_notification.yaml)

---

### Fase 5: Documentazione Doppia (Lapidaria & Tecnica) e Rilascio
- [x] **Guida Lapidaria / Quickstart in 3 passi** in [`docs/QUICKSTART.md`](docs/QUICKSTART.md)
- [x] **Documentazione Tecnica Completa** in [`docs/HARDWARE.md`](docs/HARDWARE.md) con pinout a 19 GPIO
- [x] **README.md principale di livello GitHub** con diagramma a 3 attori e badge My Home Assistant
- [x] Licenza MIT in [`LICENSE`](LICENSE)
- [x] Note di rilascio ufficiali in [`RELEASE_NOTES.md`](RELEASE_NOTES.md)
- [x] Wiki GitHub online con sidebar e 6 guide dedicate
- [x] Release ufficiale `v1.0.0` pubblicata su GitHub

---

### Fase 6: Portfolio & Engineering Excellence (Senior Dev Quality)

#### 1. ⚙️ CI/CD & Pipeline di Automazione
- [x] Workflow GitHub Actions (`.github/workflows/ci.yml`) per validazione automatica configurazione ESPHome su push e PR
- [x] Linting automatico dei file YAML (`yamllint` con `.yamllint.yml`) integrato nel workflow CI
- [x] Badge di build passing nel `README.md` (`ESPHome CI | Passing`)

#### 2. 🧠 ADR (Architecture Decision Records)
- [x] Documento [`docs/ARCHITECTURE_DECISIONS.md`](docs/ARCHITECTURE_DECISIONS.md) con le motivazioni ingegneristiche:
  - **ADR-01**: Bus SPI a 80MHz vs 40MHz (calcolo del pixel throughput e timing budget a 30fps)
  - **ADR-02**: Passive BLE Proxy vs Active (co-esistenza RF a 2.4GHz, RAM saving e prevenzione underrun audio I2S)
  - **ADR-03**: Dynamic Power Gating su GPIO46 (risparmio corrente di riposo amplificatore Classe D e azzeramento fruscio)
  - **ADR-04**: Coda notifiche LIFO a 10 elementi (priorità visiva per messaggistica domotica immediata)
  - **ADR-05**: Sincronizzazione SNTP a evento (azzeramento del wake lock ciclico della CPU rispetto al polling a 10s)

#### 3. 🤝 Standard Open Source & Dev Experience (DX)
- [x] Guida per i contributori in [`CONTRIBUTING.md`](CONTRIBUTING.md) (setup locale, convenzione commit, branch workflow)
- [x] Template per le Issue di GitHub (`.github/ISSUE_TEMPLATE/`):
  - Form guidato per Bug Report (`bug_report.yml`)
  - Form guidato per Feature Request (`feature_request.yml`)
  - Configurazione link esterni (`config.yml`)
- [x] Template per le Pull Request (`.github/pull_request_template.md`) con checklist di verifica e garanzia parità
- [x] Codice di condotta Contributor Covenant v2.1 in [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md)

#### 4. 📸 Visual Showcase & Media Reali
- [x] Cartella `.github/assets/` con specifiche multimediali e guida asset
- [x] Foto reali del device in funzione (`smartdisplay_standby_clock.jpg`, `smartdisplay_notification_card.jpg`, `smartdisplay_clear_dialog.jpg`, `smartdisplay_system_info.jpg`)
- [x] Galleria fotografica integrata nel `README.md` e nella Wiki
- [x] Immagine banner per anteprima social su GitHub (`.github/assets/banner.png` 1280x640)
- [ ] Registrazione GIF animate del display funzionante per `README.md` (schermata standby, animazione notifica, gesture touch)

#### 5. 🌐 Condivisione Community
- [x] Preparazione scheda di sottomissione per [devices.esphome.io](https://devices.esphome.io) in [`docs/community/devices_esphome_io_submission.md`](docs/community/devices_esphome_io_submission.md)
- [x] Redazione post di presentazione per Home Assistant Community in [`docs/community/home_assistant_community_post.md`](docs/community/home_assistant_community_post.md)
- [x] Sottomissione PR ufficiale al repository [`esphome/devices.esphome.io#1916`](https://github.com/esphome/devices.esphome.io/pull/1916)
- [x] Pubblicazione del thread sul forum di Home Assistant
